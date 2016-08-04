+++
date = "2016-08-04T13:38:10+09:00"
draft = false
tags = ["Go", "GCP"]
title = "【Go】Go Bookshelf App: Cloud Storage"

+++

続いて、["Using Cloud Storage with Go"](https://cloud.google.com/go/getting-started/using-cloud-storage)。

### Cloud Storageの作成

下記コマンドでCloud Storageを作成する。  
実はどんな名前でもいいんだけど、ProjectIDに合わせておくのがよい。

```
❯❯❯ gsutil mb gs://*****-***-kotazi                                            
Creating gs://*****-***-kotazi/...
~/R/blog ❯❯❯ gsutil defacl set public-read gs://*****-***-kotazi
Setting default object ACL on gs://*****-***-kotazi/...
```


### Configuring settings

プロジェクトルートへ移動して`config.go`を開く。

```
cd $GOPATH/src/github.com/GoogleCloudPlatform/golang-samples/getting-started/bookshelf
```

下記部分のコメントを消して、バケット名に書き換える。

```
// StorageBucket, err = configureStorage("<your-storage-bucket>")
```

ローカル立ち上げやデプロイの方法はさっきと同じ。

![](https://cloud.google.com/go/images/go-binary-data.png)


今回で言うと本棚の画像をCloud Storage側におけるようになる。そのためのコードが下記。

`getting-started/bookshelf/app/app.go`の`uploadFileFromForm`関数。
この関数の中で画像があるかないかをチェックした折、あればCloud Storageにアップロードする。

```
// uploadFileFromForm uploads a file if it's present in the "image" form field.
func uploadFileFromForm(r *http.Request) (url string, err error) {
        f, fh, err := r.FormFile("image")
        if err == http.ErrMissingFile {
                return "", nil
        }
        if err != nil {
                return "", err
        }

        if bookshelf.StorageBucket == nil {
                return "", errors.New("storage bucket is missing - check config.go")
        }

        // random filename, retaining existing extension.
        name := uuid.NewV4().String() + path.Ext(fh.Filename)

        ctx := context.Background()
        w := bookshelf.StorageBucket.Object(name).NewWriter(ctx)
        w.ACL = []storage.ACLRule{{Entity: storage.AllUsers, Role: storage.RoleReader}}
        w.ContentType = fh.Header.Get("Content-Type")

        // Entries are immutable, be aggressive about caching (1 day).
        w.CacheControl = "public, max-age=86400"

        if _, err := io.Copy(w, f); err != nil {
                return "", err
        }
        if err := w.Close(); err != nil {
                return "", err
        }

        const publicURL = "https://storage.googleapis.com/%s/%s"
        return fmt.Sprintf(publicURL, bookshelf.StorageBucketName, name), nil
}
```

`uploadFileFromForm`の呼び出しは`app.go`の`bookFromForm`

```
// bookFromForm populates the fields of a Book from form values
// (see templates/edit.html).
func bookFromForm(r *http.Request) (*bookshelf.Book, error) {
        imageURL, err := uploadFileFromForm(r)
        if err != nil {
                return nil, fmt.Errorf("could not upload file: %v", err)
        }
        if imageURL == "" {
                imageURL = r.FormValue("imageURL")
        }

        book := &bookshelf.Book{
                Title:         r.FormValue("title"),
                Author:        r.FormValue("author"),
                PublishedDate: r.FormValue("publishedDate"),
                ImageURL:      imageURL,
                Description:   r.FormValue("description"),
                CreatedBy:     r.FormValue("createdBy"),
                CreatedByID:   r.FormValue("createdByID"),
        }
```

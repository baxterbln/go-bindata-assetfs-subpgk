# go-bindata-assetfs-subpgk

Serve embedded files from [jteeuwen/go-bindata](https://github.com/jteeuwen/go-bindata) with `net/http`.

[GoDoc](http://godoc.org/github.com/elazarl/go-bindata-assetfs)

### Installation

Install with

    $ go get github.com/jteeuwen/go-bindata/...
    $ go get github.com/elazarl/go-bindata-assetfs/...

### Creating embedded data

Usage is identical to [jteeuwen/go-bindata](https://github.com/jteeuwen/go-bindata) usage,
instead of running `go-bindata` run `go-bindata-assetfs`.

The tool will create a `bindata_assetfs.go` file, which contains the embedded data.

A typical use case is

    $ go-bindata -pkg="assets" data/...
	$ go-bindata-assetfs -pkg="assetsfs" data/...
	
Move the files in you subfolders, for example "system/assets/" and "system/assetfs/"

### Using assetFS in your code

The generated file provides an `assetFS()` function that returns a `http.Filesystem`
wrapping the embedded files. What you usually want to do is:

For a simple fileserver:

    http.Handle("/", http.FileServer(assetfs.AssetFS()))
	
To use only for css, js eg...
	
	import "./system/assetfs"
	
	http.Handle("/js/", http.FileServer(assetfs.AssetFS()))
	http.Handle("/css/", http.FileServer(assetfs.AssetFS()))

This would run an HTTP server serving the embedded files.

To use it with html/template, a example:

	func RenderTemplate(w http.ResponseWriter, templates []string, name string, data interface{}, app string) error {

		// Load Template Routines
		finalTemplate := template.New(name)
	
		//get parse template files and load it from the Assets
		for _, file := range templates {
			asset, err := assets.Asset(file)
			if err != nil {
				panic(err)
			}
			finalTemplate.Parse(string(asset))
		}
	
		// Execute the template
		err := finalTemplate.ExecuteTemplate(w, name, data)
		if err != nil {
			return err
		}
	
		return nil
	}
	
	func GetBaseTemplates() []string {
		templates := []string{"views/base/footer.html", "views/base/header.html", "views/base/navbar.html", "views/base.html"}
		return templates
	}

## Without running binary tool

You can always just run the `go-bindata` tool, and then

use

     import "github.com/baxterbln/go-bindata-assetfs-subpkg"
     ...
     http.Handle("/",
        http.FileServer(
        &assetfs.AssetFS{Asset: Asset, AssetDir: AssetDir, Prefix: "data"}))



to serve files embedded from the `data` directory.

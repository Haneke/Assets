# Haneke Swift

### github.com/Haneke


---

![fit](http://pixel.nymag.com/imgs/daily/vulture/2012/12/07/07-michael-haneke.o.jpg/a_560x0.jpg)

^This is filmmaker Michael Haneke, director of movies like Funny Games, Amour and Cache.

---

![inline](https://raw.githubusercontent.com/Haneke/Assets/master/Design/haneke-icon-rounded-1024.png)

^This is Haneke, an open source iOS cache library for Swift and Objective-C.

---

# Lightweight *generic* cache for iOS

```swift
let cache = Cache<JSON>(name: "github")

let URLString = "https://api.github.com/users/haneke"
let URL = NSURL(string: URLString)!
    
cache.fetch(URL: URL).onSuccess { JSON in
   println(JSON.dictionary?["bio"])
}
```

^ It's designed to be super-simple to use. Here's how you would initalize a JSON cache and fetch objects from a url.

---

# Cache all the types!

Memory and LRU disk cache for:

* `UIImage`
* `NSData`
* `JSON`
* `String`
* Or any other type that can be read or written as data

---

#Features

* Generic cache with out-of-the-box support for `UIImage`, `NSData`, `JSON` and `String`
* First-level memory cache using `NSCache`
* Second-level LRU disk cache using the file system
* Asynchronous fetching from network or disk

---

#More features

* All disk access is performed in background
* Thread-safe
* Automatic cache eviction on memory warnings or when the disk capacity is reached

--- 

# Image features

* Zero-config `UIImageView` and `UIButton` extensions
* Optimized for `UITableView` and `UICollectionView` cell reuse
* Background image resizing and decompression

<br>


```swift
imageView.hnk_setImageFromURL(url)
```

---

#Meta-features

* Convention over configuration
* Comprehensive unit tests
* Extensible
* Easy debugging
* ~~Fully documented~~

---

# Requirements

Zero stress policy:

- iOS 8.0+
- Xcode 6.1


---

#Using the cache

```Swift
let cache = Haneke.sharedDataCache
        
cache.set(value: data, key: "funny-games.mp4")
        
// Eventually...

cache.fetch(key: "funny-games.mp4").onSuccess { data in
    // Do something with data
}
```

---

# Fetching

Fetch operation can also set if a source is provided.

```Swift
let cache = Haneke.sharedJSONCache

let URLString = "https://api.github.com/users/haneke"
let URL = NSURL(string: URLString)!
    
cache.fetch(URL: URL).onSuccess { JSON in
   println(JSON.dictionary?["bio"])
}
```

---

```Swift
cache.fetch(URL: URL).onSuccess { JSON in }
```

1. Fetch the required JSON from (in order) memory, disk or `NSURLCache`. 
2. If not available, fetch the JSON from the url, parse it and return it asynchronously.
3. And immediately afterwards cache the JSON in background.

---

##Extra ♡ for images

Smart UIKit categories.

```swift
// Setting a remote image
imageView.hnk_setImageFromURL(url)

// Setting an image manually. Requires you to provide a key.
imageView.hnk_setImage(image, key: key)
```

---

The above lines take care of:

* *If cached*, retrieving an *appropriately sized image* from the memory or disk cache (in background).
* *If not cached*, loading the original from its source and producing an appropriately sized image (both in background). 
* Remote images will also be retrieved from the shared `NSURLCache` if available.

---

Then:

* Setting the image and animating the change if appropriate.
* Or doing nothing if the `UIImageView` was reused during any of the above steps.
* Caching the resulting image.
* If needed, evicting the least recently used images in the cache.

---

# If you need more control

```Swift
public func hnk_setImageFromURL(URL: NSURL, 
	placeholder : UIImage? = nil, 
	format : Format<UIImage>? = nil, 
	failure fail : ((NSError?) -> ())? = nil, 
	success succeed : ((UIImage) -> ())? = nil) { 
... 
}
```

---

# Extending Haneke

## Formats
## Fetchers
## Adding types

---

#Formats

Allow transformations to the values before being cached. 

For example, the `UIImageView` extension uses a format that resizes images to fit or fill the image view as needed.

---

# Format example

```swift
let cache = Haneke.sharedImageCache

let iconFormat = Format<UIImage>(name: "icons") { image in
    return imageByRoundingCornersOfImage(image)
}
cache.addFormat(iconFormat)

let URL = NSURL(string: "http://haneke.io/icon.png")!
cache.fetch(URL: URL, formatName: "icons").onSuccess { image in
    // image will be a nice rounded icon
}
```

---

Formats can also be used from the `UIKit` extensions:

```swift
imageView.hnk_setImageFromURL(url, format: iconFormat)
```

---

##Fetchers

The `fetch` functions for urls and paths are actually convenience methods. 

Under the hood Haneke uses fetcher objects:

```swift
let URL = NSURL(string: "http://haneke.io/icon.png")!

let fetcher = NetworkFetcher<UIImage>(URL: URL)
cache.fetch(fetcher: fetcher).onSuccess { image in
    // Do something with image
}
```
---

Two specialized fetchers: 

* `NetworkFetcher<T>`
* `DiskFetcher<T>`. 

You can also implement your own fetchers by subclassing `Fetcher<T>`.

---

#Custom fetchers

Fetch original values from other sources than network or disk (e.g., Core Data)

Or even change how Haneke acceses network or disk (e.g., use [Alamofire](https://github.com/Alamofire/Alamofire) for networking instead of `NSURLSession`). 

---

# Implementing custom fetchers

A custom fetcher must subclass `Fetcher<T>`. Responsibilities:

* Providing the key associated with the value to be fetched (e.g., `NSURL.absoluteString` in the case of `NetworkFetcher`)
* Fetching the value in background and calling the success or failure closure (both in the main queue)
* Cancelling the fetch on demand, if possible

Type restriction: must implement `DataConvertible`. 

---

#Supporting additional types

By implementing protocols:

```Swift
public protocol DataConvertible {
    typealias Result
    
    class func convertFromData(data:NSData) -> Result?
}

public protocol DataRepresentable {
    func asData() -> NSData!
}
```

---

# E.g., support for `NSDictionary`

```Swift
extension NSDictionary : DataConvertible, DataRepresentable {
    public typealias Result = NSDictionary
    
    public class func convertFromData(data:NSData) -> Result? {
        return NSKeyedUnarchiver.unarchiveObjectWithData(data) 
        	as? NSDictionary
    }
    
    public func asData() -> NSData! {
        return NSKeyedArchiver.archivedDataWithRootObject(self)
    }
}
```

---

Then creating a `NSDictionary` cache would be as simple as:

```swift
let cache = Cache<NSDictionary>(name: "dictionaries")
```

---

# Current efforts

* Format dependencies
* Cache expiration
* Documentation
* Preloading

---

#Contributors

@joanromano
@oriolblanc
@hpique
@lascorbe

---

###github.com/Haneke

^If you would like to join us in learning Swif, and contribute to an active cache library, we invite you to visit github dot com slash Haneke, fork the project and submit pull requests.

^Thank you!


---

![inline](https://raw.githubusercontent.com/Haneke/Assets/master/Design/haneke-icon-rounded-1024.png)



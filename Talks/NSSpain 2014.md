# Learning *Swift* by developing *Haneke*

---

![fit](http://pixel.nymag.com/imgs/daily/vulture/2012/12/07/07-michael-haneke.o.jpg/a_560x0.jpg)

^This is filmmaker Michael Haneke, director of movies like Funny Games, Amour and Cache.

---

![inline](https://raw.githubusercontent.com/Haneke/Assets/master/Design/haneke-icon-rounded-1024.png)

*github.com/Haneke*

^This is Haneke, an open source iOS image cache library.

---

A lightweight *zero-config* image cache for iOS.

<br>

*`[imageView hnk_setImageFromURL:url];`*

^It's lightweight and requires no configuration. To use it, you only need one line.

---

* *Asynchronous* image retrieval
* First-level *memory cache* using NSCache
* Second-level LRU *disk cache* using the file system
* Background image *resizing*
* Image *decompression*
* Optimized for UITableView and UICollectionView *cell reuse*

^A very powerful line. It will fetch the image asynchronously, if possible from a memory cache or a disk cache. If not cached, the image will downloaded and resized to fit the image view, all in background.

---

#Haneke 1.0.0

* *UIButton* category
* Independent LRU disk cache
* Optional *AFNetworking* integration
* Audited APIs and documentation
* Fully unit tested (*until proven otherwise*)


#Available today

![right](http://i.huffpost.com/gen/948355/thumbs/o-MICHAEL-HANEKE-TWITTER-facebook.jpg)

^For those who already use, I'm excited to announce that Haneke reaches version one right here at NSSpain.

^With goodies such as a UIButton category, a NSData disk cache that can be used independently, optional AFNetworking integration, and much more.

---

@oriolblanc @joanromano @lascorbe @hpique 

![inline](https://avatars3.githubusercontent.com/u/7575099)

github.com/realm/summer-of-swift

^This summer JP's company Realm invited developers to learn Swift by doing. Oriol Blanc, Joan Romano, Luis Ascorbe and myself, Hermes Pique, decided to rethink Haneke in pure Swift. 

---
<br>
# [fit] Haneke<T>
v0.1.0

^Also today, we launch Haneke for Swift version 0.1.0.

---

A lightweight *generic* cache for iOS, in *Swift*.

<br>

*`let cache = Cache<UIImage>("images")`*

<br>

With all the features of its Objective-C counterpart*

<br>

*`imageView.hnk_setImageFromURL(url)`*

^It's a lightweight generic cache for iOS, with all the features of the Objective-C counterpart, some of which are still in progress.

---

*`let cache = Cache<UIImage>("images")`*

*`let cache = Cache<NSData>("data")`*

*`let cache = Cache<String>("text")`*


![inline](http://ocio.lne.es/img_contenido/noticias/2012/09/114998/2012_09_05_IMG_2012_09_05_153A133A41_hanecke_27.jpg)

^So now you can cache images, data, strings, or anything that can be saved to disk.

---

#Featuring

^It's all pure Swift code. Let me show you a little bit how that looks like.

---

##SQL generics

```swift
class Cache<T : DataConvertible where T.Result == T> {...}

protocol DataConvertible {
    typealias Result
    class func convertFromData(data:NSData) -> Result?
}
```

^We have what I like to call SQL generics. Here we use a requirement on the associated type of a protocol applied to a generic type paramater, as a workaround to Swift limitations on protocols that reference the implementing type.

^And it's ok if you don't understand this. We don't understand it either.

stackoverflow.com/questions/25922152/not-identical-to-self
stackoverflow.com/questions/25915306/generic-closure-in-protocol

---

##Mike Ash code

```swift
func getKey() -> COpaquePointer {
    let ptr: COpaquePointer =
    Unmanaged<AnyObject>.passUnretained(self).toOpaque()
    return ptr
}
```

^We also have code that would make Mike Ash proud.

---

#The amazing underscore

BAD

```swift
dispatch_sync(sut.cacheQueue, {
    fileManager.removeItemAtPath(path, error: &error)
})
```

*GOOD*


```swift
dispatch_sync(sut.cacheQueue, {
    let _ = fileManager.removeItemAtPath(path, error: &error)
})
```

^We learned to love the underscore. It's great to ignore variables, but sometimes necessary to help Swift's type inference.

^For example, here Swift sees that removeItemAtPath returns a Bool and infers that the closure does the same. The let underscore allows us to ignore the Bool and tell Swift the closure returns Void.

---

##Mutant Ninja Tuples

```swift
func onMemoryWarning() {
    for (_, (_, memoryCache, _)) in self.formats {
        memoryCache.removeAllObjects()
    }
}
```

^And of course, we abuse tuples. They're great to save boilerplate internal code.

^Here we iterate over a dictionary of tuples, and leveraging the amazing underscore we only focus on the part of the tuple we need.

--- 

##And more

* *@autoclosure*s to optimize performance
* test *expectations*
* *mocking* with function classes
* *bitwise* operations
* *structs* as *namespaces*
* *extensions*
* interaction with *C APIs*

![right](http://htmlgiant.com/wp-content/uploads/2010/01/hanekejolly.jpg)

^This barely scratches the surface. We used lots of other Swift things and techniques, such as autoclosures, mocking, structs as namespaces, and more.

--- 

#Takeaways

![inline](http://www.justsuperstar.com/michael_haneke/photo/haneke4.jpg)

^So, what did we learn?

---
#Swift is not *finished*

![left](https://vjmorton.files.wordpress.com/2007/09/michael_haneke-1.jpg)

^We learned that Swift is far from finished.

---

BAD

*`let image = UIImage(data: data)`*

*GOOD*

*`if let image : UIImage! = UIImage(data: data) { ... }`*

^Be prepared for a lot of workarounds and hacks like this.

^For example, Objective C inits might return nil, even if the interface say otherwise.

---

#Change *beyond* functional

![inline](http://www.news.at/_storage/asset/1057014/storage/newsat:key-visual/file/12300274/michael-haneke-der-meister-verstoerung-322863_e.jpg)

^But most importantly, we learned that learning Swift is more than learing functional programming.

---

from
# [fit] mutability
to
# [fit] *inmutability*

^It's a shift from mutability to inmutability.

---

from
# [fit] classes
to
# [fit] *Any*

^From classes to pretty much whatever you want.
 
 ---

from
# [fit] verbosity
to
# [fit] *inference*

^And from verbosity to inference.

---

![inline](https://raw.githubusercontent.com/Haneke/Assets/master/Design/haneke-icon-rounded-1024.png)

*github.com/Haneke*

^If you would like to join us in learning Swif, and contribute to a kick-ass cache library, we invite you to visit github dot com slash Haneke, fork the project and submit pull requests.

^Thank you!

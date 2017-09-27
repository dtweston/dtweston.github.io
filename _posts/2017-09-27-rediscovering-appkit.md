---
layout: post
title: "Rediscovering AppKit"
date: 2017-09-27
---
I'm currently working on new features for my app [Netopsy] [netopsy-itunes], and I thought
it would be a good idea to keep track of my progress.

Netopsy is an app that lets you view network traces from Fiddler.NET. While working on the 
Yammer team at Microsoft, I occasionally received network traces and discovered that while
the format is pretty simple, there was no easy way to view them on a Mac.

The first task on my plate is to build a control to implement a list filter. My inspiration
is a similar control from Xcode:

![Xcode list filter widget](/assets/inspiration.png)

AppKit provides an `NSSearchField` control, however, I wasn't sure how to customize the search
button icon to look more like a filter, and I wasn't sure if it would support having additional
buttons on the right-hand side.

So...

# Off to playgrounds we go!

The pieces we need to assemble are: an `NSControl` for the top-level container, and inside
that, from left to right, `NSButton`, `NSTextField`, `NSButton`, `NSButton`.

We'll start with the following code:

``` swift
class CustomSearchField: NSControl {
    let searchButton: NSButton
    let searchField: NSTextField
    let filter1Button: NSButton
    let filter2Button: NSButton

    required init?(coder: NSCoder) {
        fatalError()
    }

    override init(frame frameRect: NSRect) {
        searchField = NSTextField()

        let searchImage = NSImage(named: NSImage.Name.actionTemplate)!
        searchButton = NSButton()
        searchButton.image = searchImage

        let filter1Image = NSImage(named: .bookmarksTemplate)!
        let filter2Image = NSImage(named: .bluetoothTemplate)!
        filter1Button = NSButton()
        filter1Button.image = filter1Image
        filter2Button = NSButton()
        filter2Button.image = filter2Image

        super.init(frame: frameRect)

        self.addSubview(searchButton)
        self.addSubview(searchField)
        self.addSubview(filter1Button)
        self.addSubview(filter2Button)

        let views: [String: NSView] = ["search": searchButton,
                                       "text": searchField,
                                       "filter1": filter1Button,
                                       "filter2": filter2Button]
        for view in views.values {
            view.translatesAutoresizingMaskIntoConstraints = false
        }
        addConstraints(NSLayoutConstraint.constraints(withVisualFormat: "H:|[search][text(>=40)][filter1][filter2]|", options: .alignAllCenterY, metrics: nil, views: views))
    }
}
```

![End of day 1](/assets/day1-progress.png)

That's... something. It's not terribly handsome, but we can build on this.

I owe much gratitude to the following URLs:
- [stackoverflow: Best way to change the background color for an NSView][nsview-bgcolor] (Is there a way
  to see how many times I've visited a particular stackoverflow question? I feel like this has to be my number one
  most revisited question and answer. (And based on the activity there - I'm not the only one.))

Radars opened:
- [Unable to create new playground in existing workspace](https://openradar.appspot.com/34666710)
- [Playground stops working](https://openradar.appspot.com/34690901)

[netopsy-itunes]: https://itunes.apple.com/us/app/netopsy/id1171312680?mt=12
[nsview-bgcolor]: https://stackoverflow.com/questions/2962790/best-way-to-change-the-background-color-for-an-nsview

---
title: "Swift Convert function"
date: 2017-05-21 13:41:00
tags: swift uikit
---

While working on an iOS app I ran into the problem that I needed to calculate the position of a view on the screen in relation to another element which was not the parent element. If this element would've been the parent element a simple way to get that position would be to just get the frame of the view: `let viewFrame = view.frame`. The frame is the position of the element in relation to the parent element.

Sometimes though you want to get the position of the element in relation to a view that is four or five parents above the one that the view is in. Why, you might ask? A good example is to see whether or not a view is visible for the user or not.

The `convert` function looks like this:

``` swift
func convert(_ rect: CGRect, to view: UIView?) -> CGRect?
```

This function will convert a CGRect that is within the bounds of the view that the function is called upon to another view which can be given as a second parameter. If this parameter is not given it will be calculated according to the window.

Let's see some code which illustrates the usefulness of this method:

``` swift
class ProductPageViewController: UIScrollViewDelegate {
  @IBOutlet private weak var scrollView: UIScrollView!
  @IBOutlet private weak var productNameView: ProductNameView!

  func scrollViewDidScroll(_ scrollView: UIScrollView) {
    let rect = productNameView.convert(productNameView.frame, to: scrollView)

    if (rect.origin.y < 0) {
      // At this moment the view begins to scroll out of the view.
    }
  }
}
```

At the moment that a particular view begins to scroll out of the view it's possible to show another view based on how far the view has been scrolled out of the way.

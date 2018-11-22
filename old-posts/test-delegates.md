---
title: "Unit Testing delegates"
date: 2017-06-08 20:12:00
tags: [swift, testing, delegate]
---

In an app that I'm currently developing I'm using delegates to communicate to other objects.
Most of the time a delegate is used if there's some sort of asynchronous operation going on.
Seeing as I like to pretend I'm doing TDD, it's quite important to have a way of testing whether or not a particular delegate is called and with the correct value.

This is a very simplified illustration of the code that I wanted to test.

``` swift
protocol SearchServiceDelegate: class {
  func didRetrieveProducts(products: [Product])
}

class SearchService {
  weak var delegate: SearchServiceDelegate?

  func searchForProducts(withSearchTerm searchTerm: String) {
    // Get products
    delegate?.didRetrieveProducts(products: products)
  }
}
```

The way that you would normally test asynchronous code with XCTest is by using the waitForExceptation function. However I wanted to find a simpler way of testing this piece of code; turns out there is one:

``` swift
class SearchServiceTests: XCTestCase {
  func testThatSearchRetrievesProductsCorrectly() {
    let searchService = SearchService()
    searchService.delegate = self

    searchService.searchForProducts(withSearchTerm: "apple")
  }
}

extension SearchServiceTests: SearchServiceDelegate {
  func didRetrieveProducts(products: [Product]) {
    XCTAssertTrue(products.count > 0)
  }
}
```

When running the test Xcode will recognize that the `didRetrieveProducts` function is called and mark it with a green checkmark or a red cross if it fails. I never expect this to work, pretty cool.

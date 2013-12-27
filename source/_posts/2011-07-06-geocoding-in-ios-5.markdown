---
layout: post
title: "iOS 5的Geocoding"
date: 2011-07-06 23:44
comments: true
categories: [Core Location, iOS 5, MapKit]
---

很明显，本文有标题党的嫌疑。其实本文要说的是“[iPhone Programming: The Big Nerd Ranch Guide](http://www.bignerdranch.com/book/ios_programming_the_big_nerd_ranch_guide_nd_edition_)”一书中第5章里的Whereami的例子。我照着书本做下来，但是感觉例子似乎有点问题。可能是我看书不仔细，不过这个例子要实现的功能很诡异——定位自己，然后自己用文本框加上一个Annotation(？)这太诡异了吧。

我现在对这个例子小做修改，那就是，在文本框中输入任何地址字符串，然后地图自动定位到该位置——类似Google Map的搜索。我基于书本上的代码，做一些小修改。

首先，注释掉`-locationManager:didUpdateToLocation:fromLocation:`这个方法里的所有代码。主要对`-findLocation`方法作出修改，实现地图搜索功能。
<!-- more --> 
``` objc
- (void)findLocation {
    CLGeocoder *geoCoder = [CLGeocoder new];
    [geoCoder geocodeAddressString:locationString completionHandler:^(NSArray *placemarks, NSError *error) {
        if (error) {
            NSLog(@"%@", [error description]);
            return;
        }
        else {
            CLPlacemark *m = [placemarks objectAtIndex:0];
            MapPoint *mp = [[MapPoint alloc] initWithCoordinate:[[m location] coordinate] title:[locationTitleField text]];
            [mapView addAnnotation:mp];
            [mp release];
            [mapView setCenterCoordinate:[[m location] coordinate] animated:YES];
            [self foundLocation];
        }
    }];
    
    [locationManager startUpdatingLocation];
    [activityIndicator startAnimating];
    [locationTitleField setHidden:YES];
}
```

这里用到了iOS5里的一个全新的类：`CLGeocoder`。现在Reverse Geocoding也能够通过这个类实现了，之前的`MKReverseGeocoder`类在iOS5里已经变成Depreciated了。获取地址使用了`CLGeocoder`类中的`– geocodeAddressString:completionHandler:`的方法。这个方法接受一个地址字符串和一个处理Block，用来在找到结果的时候对结果进行处理。简单起见，上面的实现里直接取了获得的所有可能位置里的第一个结果。在实际的实现中应该将所有可能的结果都展示给用户，让用户决定选择正确的位置。

iOS 5的`MapKit`和`CoreLocation`框架都有比较大的变化，所以有可能的话可以了解一下API变化是非常有好处的——因为API变得越来越好用了。

（全文完）
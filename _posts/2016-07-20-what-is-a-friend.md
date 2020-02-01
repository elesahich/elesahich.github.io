---
title: What is a friend?
date: 2016-07-20
description: Exploring the mechanics of friendship
categories:
  - friends
image: https://source.unsplash.com/collection/375719/2000x1322?a=.png
author_staff_member: anna
---

Friendship is a relationship of mutual affection between people. Friendship is a stronger form of interpersonal bond than an association. Friendship has been studied in academic fields such as sociology, social psychology, anthropology, and philosophy. Various academic theories of friendship have been proposed, including social exchange theory, equity theory, relational dialectics, and attachment styles. A World Happiness Database study found that people with close friendships are happier.

![Checkmate](https://source.unsplash.com/random/1500x1000)

## Forms of friendship

Although there are many forms of friendship, some of which may vary from place to place, certain characteristics are present in many types of bond. Such characteristics include affection, sympathy, empathy, honesty, altruism, mutual understanding, and compassion, enjoyment of each other's company, trust, and the ability to be oneself, express one's feelings, and make mistakes without fear of judgment from the friend.

While there is no practical limit on what types of people can form a friendship, friends tend to share common backgrounds, occupations, or interests and have similar demographics.

![Raspberries](https://source.unsplash.com/random/1500x1001)

```swift
//
//  AirbnbPageTabNavigationView.swift
//  airbnb-main
//
//  Created by Yonas Stephen on 29/3/17.
//  Copyright © 2017 Yonas Stephen. All rights reserved.
//

import UIKit

protocol AirbnbPageTabNavigationViewDelegate {
    func didSelect(tabItem: AirbnbPageTabItem, atIndex index: Int, completion: (() -> Void)?)
}

class AirbnbPageTabNavigationView: UIScrollView {
    var navigationDelegate: AirbnbPageTabNavigationViewDelegate?
    var pageTabItems = [AirbnbPageTabItem]()

    var titleColor: UIColor = UIColor.white {
        didSet {
            for item in pageTabItems {
                item.titleColor = titleColor
            }
        }
    }
    var selectedTitleColor: UIColor = UIColor.white {
        didSet {
            for item in pageTabItems {
                item.selectedTitleColor = selectedTitleColor
            }
            selectedLineView.backgroundColor = selectedTitleColor
        }
    }

    var selectedTabItem: AirbnbPageTabItem? {
        didSet {
            pageTabSelected()
        }
    }
    var selectedLineCenterXAnchor: NSLayoutConstraint?
    var selectedLineWidthAnchor: NSLayoutConstraint?
    var selectedLineView: UIView = {
        let view = UIView()
        view.translatesAutoresizingMaskIntoConstraints = false
        view.backgroundColor = UIColor.white
        return view
    }()

    var backgroundGradientLayer: CAGradientLayer {
        let gradient = CAGradientLayer()
        gradient.startPoint = CGPoint(x: 0, y: 0.5)
        gradient.endPoint = CGPoint(x: 1, y: 0.5)
        gradient.colors = [Theme.PRIMARY_COLOR.cgColor, Theme.PRIMARY_DARK_COLOR.cgColor]
        return gradient
    }

    override var bounds: CGRect {
        didSet {
            backgroundGradientLayer.frame = bounds
        }
    }

    init() {
        super.init(frame: CGRect.zero)

        backgroundColor = Theme.PRIMARY_DARK_COLOR

        setupViews()
    }

    required init?(coder aDecoder: NSCoder) {
        fatalError("init(coder:) has not been implemented")
    }


    override func layoutSubviews() {
        //        if backgroundGradientLayer.superlayer == nil {
        //            layer.insertSublayer(backgroundGradientLayer, at: 0)
        //        }
    }

    func setupViews() {
        showsHorizontalScrollIndicator = false
        alwaysBounceHorizontal = true

        addSubview(selectedLineView)

        selectedLineView.heightAnchor.constraint(equalToConstant: 2).isActive = true
        // TODO: figure out why using bottomAnchor does not work
        selectedLineView.topAnchor.constraint(equalTo: topAnchor, constant: 50 - 2).isActive = true
        selectedLineCenterXAnchor = selectedLineView.centerXAnchor.constraint(equalTo: centerXAnchor)
        selectedLineCenterXAnchor?.isActive = true
        selectedLineWidthAnchor = selectedLineView.widthAnchor.constraint(equalTo: widthAnchor)
        selectedLineWidthAnchor?.isActive = true
    }

    func appendPageTabItem(withTitle title: String) {

        let prevTabItem: AirbnbPageTabItem? = pageTabItems.last

        let tabItem = AirbnbPageTabItem()
        tabItem.translatesAutoresizingMaskIntoConstraints = false
        tabItem.title = title
        tabItem.delegate = self

        addSubview(tabItem)

        tabItem.leftAnchor.constraint(equalTo: prevTabItem != nil ? prevTabItem!.rightAnchor : leftAnchor).isActive = true
        tabItem.topAnchor.constraint(equalTo: topAnchor).isActive = true
        tabItem.heightAnchor.constraint(equalTo: heightAnchor, constant: -2).isActive = true
        let width = tabItem.title.width(withConstrainedHeight: frame.size.height, font: tabItem.titleButton.titleLabel!.font)
        tabItem.widthAnchor.constraint(equalToConstant: ceil(width) + 50).isActive = true

        pageTabItems.append(tabItem)
    }
}

extension AirbnbPageTabNavigationView: AirbnbPageTabItemDelegate {
    func didSelect(tabItem: AirbnbPageTabItem, completion: (() -> Void)?) {
        if let index = pageTabItems.index(of: tabItem) {
            navigationDelegate?.didSelect(tabItem: tabItem, atIndex: index) {

                self.animatePageTabSelection(toIndex: index)

                if let handler = completion {
                    handler()
                }
            }
        }
    }

    func animatePageTabSelection(toIndex index: Int) {
        if index >= 0, index < self.pageTabItems.count {
            selectedTabItem = self.pageTabItems[index]
        }
    }

    func pageTabSelected() {
        for item in pageTabItems {
            if item != selectedTabItem {
                item.isSelected = false
            }
        }

        selectedTabItem?.isSelected = true

        if  selectedTabItem != nil {
            self.layoutIfNeeded()
            UIView.animate(withDuration: 0.2, animations: {
                self.selectedLineCenterXAnchor?.isActive = false
                self.selectedLineCenterXAnchor = self.selectedLineView.centerXAnchor.constraint(equalTo: self.selectedTabItem!.centerXAnchor)
                self.selectedLineCenterXAnchor?.isActive = true

                self.selectedLineWidthAnchor?.isActive = false
                self.selectedLineWidthAnchor = self.selectedLineView.widthAnchor.constraint(equalTo: self.selectedTabItem!.widthAnchor, constant: -30)
                self.selectedLineWidthAnchor?.isActive = true

                self.layoutIfNeeded()
            })
        }
    }
}
```

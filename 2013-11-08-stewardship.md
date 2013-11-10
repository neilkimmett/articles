---
layout: post
title: Stewardship
framework: ""
rating: 0.0
description: ""
---

Open Source communities function within what economists describe as a Gift Economy. Rather than paying one another for goods or services through barter or currency, everyone shares with one another, and gains social currency based on their generosity. It's similar to how friends tend to take turns inviting one another over for dinner or a party.

With the negligable cost of distributing software over the internet, developers are able to participate with millions of others around the world. And as a result, we have been able to collaboratively build amazing software, creating wealth and helping one another.

In terms of open source participation, releasing code is only one aspect—and arguably not even the most important one at that. Developing an open source project requires equal parts engineering, product design, communication, and community management. But the true deciding factor for whether an open source project succeeds is **stewardship**.

Stewardship is an old word. It evokes the ethic of public service and duty. To be a steward is to embody the responsibilities that come with ownership. It is an act that justifies authority through continued accountability; both the greatest challenge and reward of creating and maintaining a project.

## Creating

It's not enough to dump a pile of source code somewhere and declare it "open source". To do so misses the point entirely. The first step of stewardship is to clearly explain the goal and value proposition of the project, and establish clear expectations going forward.

### README

A README is the most important part of any open source project. It describes why someone would want to use the code, and how they may start to do so.

No matter what the programming language, size, or level of abstraction, good READMEs have the following:

- A short, one or two sentence introduction that clearly explains what the project is in simple, understandable language.
- A section describing the basic usage of the primary tasks of the project. For example, a UI component would provide sample code of how to create, configure, and add itself to a view.
- A list of requirements and instructions on how to install the code into one's project.
- Links to documentation and additional resources for more information.
- Contact information for the author or current maintainer of the project.
- A quick statement about the licensing terms of the code in the project. 

### LICENSE

All open source code should be released under an appropriate license. Unless you have a really good reason not to, choose from any of the licenses approved by the Open Source Initiative, like MIT, Apache 2.0, or GPL.

> If you're unsure which license to choose, there are several resources online that you can use to learn more. Most open source Objective-C projects are released under an MIT license, which is known to be compatibile with the terms of distribution for the App Store.

### Screenshot

For projects with any kind of user interface, such as a custom control, view, or animation, posting a screenshot should be considered a requirement.

Buying anything "sight unseen" is a bad idea, and the same goes for open source consumers. Although there are no monetary costs involved, evaluating a project requires a nontrivial investment in time and energy. A screenshot helps potential consumers decide if your code is worth checking out.

### Demo

Actions speak louder than words. And no matter how comprehensive a README file is, any open source project can be improved with a working example.

There's just something about seeing the code in a real context that allows developers to grok what's going on. It's also nice to have a starting point for tinkering around.

At the very least, the example can be used to bootstrap the process of fixing bugs or developing new features, both for you and for anyone who wants to contribute. It's also a great place to incorporate any testing infrastructure for the project.

### Distribution

One of the great developments in the Objective-C open source community—and in many ways, what has allowed it to flourish as it has in the first place—is CocoaPods.

CocoaPods is the de facto dependency manager for integrating third party code in iOS and Mac OS X projects. At this point, it's pretty much expected that any library worth its salt is distributed with a `.podspec`.

For the uninitiated, creating a Podspec is pretty straightforward:

#### NSHipsterKit.podspec

~~~{ruby}
Pod::Spec.new do |s|
  s.name     = 'NSHipsterKit'
  s.version  = '1.0.0'
  s.license  = 'MIT'
  s.summary  = "A pretty obscure library. You've probably never heard of it."
  s.homepage = 'http://nshipster.com'
  s.authors  = { 'Mattt Thompson' => 'mattt@nshipster.com' }
  s.source   = { :git => 'https://github.com/nshipster/NSHipsterKit.git', :tag => '1.0.0' }
  s.source_files = 'NSHipsterKit'
end
~~~

Once the `.podspec` has been submitted to the CocoaPods specs repository, a consumer would be able to add it to their own project with the following Podfile:

#### Podfile

~~~{ruby}
platform :ios, '7.0'
pod 'NSHipsterKit', '~> 1.0'
~~~

## Maintaining

### Versioning

### Answering Questions

### Soliciting Contribution

### Quality Assurance

- Answer questions on Stack Overflow
- Be responsive to issues
- Release often according to SemVer
- Being conservative in adding new features
- Quality Assurance
- 

## Transitioning

The fate of any successful enterprise is to outgrow its original creators. While this may be a troubling or unwelcome notion, it is nevertheless something that any responsible creator should keep in mind. 

- Incorporating new contributors
- Delegating repsonsibilities
- 

* * *

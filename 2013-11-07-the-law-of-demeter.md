---
layout: post
title: The Law of Demeter
framework: ""
rating: 0.0
description: ""
---

Information is power
Power corrupts.
Good fences make good neighbors.

Also known as the "principle of least knowledge", The Law of Demeter is a design guideline that advocates for functionality to be loosely coupled with one another. Essentially: objects are best when they know least about other objects. Every part of your application should be ruthlessly modularized.

Limiting the number of incoming & outgoing connections between components in a system is the only viable strategy for managing complexity as an application scales.

Central to this design philosophy is the practice of "information hiding". By playing things close to the vest, a developer affords themselves the flexibility to change implementation details later.

Languages in the C family are naturally aligned with this philosophy, because of their explicit separation of interface (`.h` files) from implementation (`.c` / `.cpp` / `.m` / `.mm`, `.cs` files). 

Classes `#import` the headers of other classes they want to interact with, but are limited to seeing only the methods, functions, and constants exposed in those headers. Anything could be going on behind the scenes... and that's sort of the point, actually. Single-responsibility objects can't be bothered to worry about how state is stored internally, or which sorting strategy is being used—it's just not in their job description.

Objective-C has a handful of unique langauge features that lend themselves to information hiding, that all professional developers should know well and use often. 

## Class Continuations


#### Person.h

~~~{objective-c}
@interface Person
@property (nonatomic, strong) NSString *name;
@property (nonatomic, strong) NSDate *birthday;
@end
~~~

#### Person.m

~~~{objective-c}
@interface Person ()
@property (readwrite, nonatomic, strong) NSString *gossip;
@end
~~~

## Redeclaring readonly Properties

#### Order.h

~~~{objective-c}
@interface Order
@property (readonly, nonatomic, strong) NSArray *items;

- (void)addItem:(Item *)item;
~~~

#### Order.m

~~~{objective-c}
@interface Order ()
@property (readwrite, nonatomic, strong) NSArray *items;
@end

@implementation Order
- (void)addItem:(Item *)item {
  self.items = [self.items arrayByAddingObject:item];
}
@end
~~~

## extern & static

#### Post.h

~~~{objective-c}
extern NSString * const XXPublicationName;

extern NSString * XXBylineForPerson(Person *person);
~~~

#### Post.m

~~~{objective-c}
NSString * const XXPublicationName = @"NSHipster Times-Picayune"

static NSString * const XXPublicationBylineFormatString = @"by %@";

NSString * XXBylineForPerson(Person *person) {
  return [NSString stringWithFormat:XXPublicationBylineFormatString, person.name];
}
~~~

## Delegates & Protocols

#### CreatePostViewController.h

~~~{objective-c}
@protocol CreatePostViewControllerDelegate
- (void)viewController:(CreatePostViewController *)viewController
         didCreatePost:(Post *)post;
@end

@interface CreatePostViewController : UIViewController
// ...
@end
~~~

#### PostsViewController.m

~~~{objective-c}
@interface PostsViewController () <CreatePostViewControllerDelegate>
@end

@implementation PostsViewController

- (IBAction)create:(id)sender {
  CreatePostViewController *viewController = [[CreatePostViewController alloc] init];
  viewController.delegate = self;

  UINavigationController *navigationController = [[UINavigationController alloc] initWithRootViewController:viewController];

  [self presentModalViewController:navigationController animated:YES];
}

#pragma mark - CreatePostViewControllerDelegate

- (void)viewController:(CreatePostViewController *)viewController
         didCreatePost:(Post *)post
{
  self.posts = [self.posts arrayByAddingObject:post];
  [self.tableView reloadData];

  [self dismissModalViewControllerAnimated:YES];
}
@end
~~~


* * *

The craft of software design shares a lot in common with good storytelling: a manageable cast of well-developed and relateable characters,  

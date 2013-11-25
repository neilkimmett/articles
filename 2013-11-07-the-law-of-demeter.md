---
layout: post
title: The Law of Demeter
framework: ""
rating: 0.0
description: ""
---

Information is power.  
Power corrupts.  
Good fences make good neighbors.  

Also known as the "principle of least knowledge", The Law of Demeter is a design guideline that advocates for functionality to be loosely coupled with one another. Essentially: objects are best when they know least about other objects. Every part of your application should be ruthlessly modularized.

Limiting the number of incoming & outgoing connections between components in a system is the only viable strategy for managing complexity as an application scales.

Central to this design philosophy is the practice of "information hiding". By playing things close to the vest, a developer affords themselves the flexibility to change implementation details later.

Languages in the C family are naturally aligned with this philosophy, because of their explicit separation of interface (`.h` files) from implementation (`.c` / `.cpp` / `.m` / `.mm`, `.cs` files). 

Classes `#import` the headers of other classes they want to interact with, but are limited to seeing only the methods, functions, and constants exposed in those headers. Anything could be going on behind the scenes... and that's sort of the point, actually. Single-responsibility objects can't be bothered to worry about how state is stored internally, or which sorting strategy is being used—it's just not in their job description.

Objective-C has a handful of unique language features that lend themselves to information hiding, that all professional developers should know well and use often. 

## Class Continuations

Class continuations, also known as class extensions, are anonymous categories that allow an implementation to privately re-declare parts of the original interface.

For example, to add a private `gossip` property to a Person class, declare it in a class continuation before the `@implementation`:

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

All properties should start out as publicly `readonly`, granting `readwrite` access only after considering how and whether a user should be able to mutate that particular aspect of state.

One common example is an object with an array-backed collection. Rather than exposing a `readwrite` interface or its mutable counterpart, the backing array is provided as `readonly`, with a method provided for adding a new item.

Although Objective-C lacks generics, this approach provides a workable type-safe solution to managing collections. It also provides a single code path for modifying the collection:

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

> A variation of this approach has an `NSMutableArray` `mutableItems` `readwrite` property in the implementation that is `@synthesize`'d in place of the `readonly` `items` property.

## extern & static

Constant variables should not publicly expose their values. This is for the safety of both API provider and API consumer. 

Revealing a magic constant can tempt users to pass the literal value in place of the reference. Keeping the value secret allows it to change in subsequent releases without breaking any code referencing the constant.

The same goes for functions, whose implementation details would be out of place in the interface, though more because of clutter rather than security.

Only variables or functions that make sense to be exposed to other classes should be declared `extern` in the interface. All internal or private members should use the `static` storage type in the implementation.

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

No part of your app should require special knowledge of any other part of your app in order to make things work.

Consider a table view that displays a list of posts, with a `+` button at the top right that pushes a modal for creating a new post. Once the form is submitted, the modal is dismissed and the new post is added to the list.

### Anti-Patterns

- On `submit:`, the form view controller creates a post and adds the post to the collection by either a reference to the list view controller, or by introspecting its presenting view controller. **This strongly couples the form to the list, whereas the form might be useful to other view controllers, such as one for displaying a single post.**
- A notification is posted when the form is submitted, which is then listened for by the list view controller, which adds the notification object to the collection. **This approach is not too bad, but notifications are more appropriate when more than one component needs to know about a particular event. A more centralized state coordinator like Core Data would be appropriate to keep track of insertions, updates, and deletes of domain objects across an application.**
- The list view controller keeps a reference to a form view controller, and adds a condition to `viewWillAppear:` that introspects the contents of the form, and adds a post from the contents if present. **This way leads to madness.**

### Correct Approach

The delegate pattern allows for a loose coupling between components, and defines a clear code path for handling the event. In this particular case, it makes sense for `EditPostViewController` to be responsible for serializing fields into properties on a `Post` object itself.

Another thing to note is that `PostsViewController` conforms to `EditPostViewControllerDelegate` in its class extension, rather than its original `@interface` declaration. This helps to cut down on the semantic clutter of the public interface.

#### CreatePostViewController.h

~~~{objective-c}
@protocol EditPostViewControllerDelegate
- (void)viewController:(EditPostViewController *)viewController
         didCreatePost:(Post *)post;
@end

@interface EditPostViewController : UIViewController
// ...
@end
~~~

#### PostsViewController.m

~~~{objective-c}
@interface PostsViewController () <EditPostViewControllerDelegate>
@end

@implementation PostsViewController

- (IBAction)create:(id)sender {
  EditPostViewController *viewController = [[EditPostViewController alloc] init];
  viewController.delegate = self;

  UINavigationController *navigationController = [[UINavigationController alloc] initWithRootViewController:viewController];

  [self presentModalViewController:navigationController animated:YES];
}

#pragma mark - EditPostViewControllerDelegate

- (void)viewController:(EditPostViewController *)viewController
         didCreatePost:(Post *)post
{
  self.posts = [self.posts arrayByAddingObject:post];
  [self.tableView reloadData];

  [self dismissModalViewControllerAnimated:YES];
}
@end
~~~

* * *

The Law of Demeter teaches us that what you don't know can't hurt you. Building your app around know-nothing components is the only way to ensure that a codebase will be able to scale and evolve over time. Approach API design from the perspective of the consumer, and you'll find that you'll have a much better time as a consumer yourself.

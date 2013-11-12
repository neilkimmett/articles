---
layout: post
title: NSFileManager
framework: "Foundation"
rating: 0.0
description: ""
---

## Common Tasks

### Determining If A File Exists

~~~{objective-c}
NSFileManager *fileManager = [NSFileManager defaultManager];
NSString *documentsPath = [NSSearchPathForDirectoriesInDomains(NSDocumentDirectory, NSUserDomainMask, YES) firstObject];
NSString *filePath = [documentsPath stringByAppendingPathComponent:@"file.txt"];
BOOL fileExists = [fileManager fileExistsAtPath:filePath];
~~~

### Listing All Files In A Directory

~~~{objective-c}
NSFileManager *fileManager = [NSFileManager defaultManager];
NSURL *bundleURL = [[NSBundle mainBundle] bundleURL];
NSArray *contents = [fileManager contentsOfDirectoryAtURL:bundleURL
                               includingPropertiesForKeys:@[]
                                                  options:NSDirectoryEnumerationSkipsHiddenFiles
                                                    error:nil];

NSPredicate *predicate = [NSPredicate predicateWithFormat:@"pathExtension ENDSWITH '.png'"];
for (NSString *path in [contents filteredArrayUsingPredicate:predicate]) {
    // Enumerate each .png file in directory
}
~~~

## Recursively Enumerating Files In A Directory

~~~{objective-c}
NSFileManager *fileManager = [NSFileManager defaultManager];
NSURL *bundleURL = [[NSBundle mainBundle] bundleURL];
NSDirectoryEnumerator *enumerator = [fileManager enumeratorAtURL:bundleURL
                                      includingPropertiesForKeys:@[NSURLNameKey, NSURLIsDirectoryKey]
                                                         options:NSDirectoryEnumerationSkipsHiddenFiles
                                                    errorHandler:^BOOL(NSURL *url, NSError *error)
{
    NSLog(@"[Error] %@ (%@)", error, url);
}];

NSMutableArray *mutableFileURLs = [NSMutableArray array];
for (NSURL *fileURL in enumerator) {
    NSString *filename;
    [fileURL getResourceValue:&filename forKey:NSURLNameKey error:nil];

    NSNumber *isDirectory;
    [fileURL getResourceValue:&isDirectory forKey:NSURLIsDirectoryKey error:nil];

    // Skip directories with '_' prefix, for example
    if ([filename hasPrefix:@"_"] && [isDirectory boolValue]) {
        [enumerator skipDescendants];
        continue;
    }

    if (![isDirectory boolValue]) {
        [mutableFileURLs addObject:fileURL];
    }
}
~~~

### Create a Directory

~~~{objective-c}
NSFileManager *fileManager = [NSFileManager defaultManager];
NSString *documentsPath = [NSSearchPathForDirectoriesInDomains(NSDocumentDirectory, NSUserDomainMask, YES) firstObject];
NSString *imagesPath = [documentsPath stringByAppendingPathComponent:@"images"];
if (![fileManager fileExistsAtPath:imagesPath]) {
    [fileManager createDirectoryAtPath:imagesPath withIntermediateDirectories:NO attributes:nil error:nil];
}
~~~

### Deleting a File

~~~{objective-c}
NSFileManager *fileManager = [NSFileManager defaultManager];
NSString *documentsPath = [NSSearchPathForDirectoriesInDomains(NSDocumentDirectory, NSUserDomainMask, YES) firstObject];
NSString *filePath = [documentsPath stringByAppendingPathComponent:@"image.png"];
NSError *error = nil;

if (![fileManager removeItemAtPath:filePath error:&error]) {
    NSLog(@"[Error] %@ (%@)", error, filePath);
}
~~~

### Determine the Creation Date of a File

~~~{objective-c}
NSFileManager *fileManager = [NSFileManager defaultManager];
NSString *documentsPath = [NSSearchPathForDirectoriesInDomains(NSDocumentDirectory, NSUserDomainMask, YES) firstObject];
NSString *filePath = [documentsPath stringByAppendingPathComponent:@"Document.pages"];

NSDate *creationDate = nil;
if ([fileManager fileExistsAtPath:filePath]) {
    NSDictionary *attributes = [fileManager attributesOfItemAtPath:filePath error:nil];
    creationDate = attributes[NSFileCreationDate];
}
~~~

#### File Attribute Keys

> - `NSFileAppendOnly`: The key in a file attribute dictionary whose value indicates whether the file is read-only.
> - `NSFileBusy`: The key in a file attribute dictionary whose value indicates whether the file is busy.
> - `NSFileCreationDate`: The key in a file attribute dictionary whose value indicates the file's creation date.
> - `NSFileOwnerAccountName`: The key in a file attribute dictionary whose value indicates the name of the file's owner.
> - `NSFileGroupOwnerAccountName`: The key in a file attribute dictionary whose value indicates the group name of the file's owner.
> - `NSFileDeviceIdentifier`: The key in a file attribute dictionary whose value indicates the identifier for the device on which the file resides.
> - `NSFileExtensionHidden`: The key in a file attribute dictionary whose value indicates whether the file's extension is hidden.
> - `NSFileGroupOwnerAccountID`: The key in a file attribute dictionary whose value indicates the file's group ID.
> - `NSFileHFSCreatorCode`: The key in a file attribute dictionary whose value indicates the file's HFS creator code.
> - `NSFileHFSTypeCode`: The key in a file attribute dictionary whose value indicates the file's HFS type code.
> - `NSFileImmutable`: The key in a file attribute dictionary whose value indicates whether the file is mutable.
> - `NSFileModificationDate`: The key in a file attribute dictionary whose value indicates the file's last modified date.
> - `NSFileOwnerAccountID`: The key in a file attribute dictionary whose value indicates the file's owner's account ID.
> - `NSFilePosixPermissions`: The key in a file attribute dictionary whose value indicates the file's Posix permissions.
> - `NSFileReferenceCount`: The key in a file attribute dictionary whose value indicates the file's reference count.
> - `NSFileSize`: The key in a file attribute dictionary whose value indicates the file's size in bytes.
> - `NSFileSystemFileNumber`: The key in a file attribute dictionary whose value indicates the file's filesystem file number.
> - `NSFileType`: The key in a file attribute dictionary whose value indicates the file's type.


> - `NSDirectoryEnumerationSkipsSubdirectoryDescendants`: Perform a shallow enumeration; do not descend into directories.
> - `NSDirectoryEnumerationSkipsPackageDescendants`: Do not descend into packages.
> - `NSDirectoryEnumerationSkipsHiddenFiles`: Do not enumerate hidden files.

## NSFileManagerDelegate

- `-fileManager:shouldMoveItemAtURL:toURL:`
- `-fileManager:shouldCopyItemAtURL:toURL:`
- `-fileManager:shouldRemoveItemAtURL:`
- `-fileManager:shouldLinkItemAtURL:toURL:`

> If you use a delegate to receive notifications about the status of move, copy, remove, and link operations, you should create a unique instance of the file manager object, assign your delegate to that object, and use that file manager to initiate your operations.

~~~{objective-c}
NSFileManager *fileManager = [[NSFileManager alloc] init];
fileManager.delegate = delegate;

NSURL *bundleURL = [[NSBundle mainBundle] bundleURL];
NSArray *contents = [fileManager contentsOfDirectoryAtURL:bundleURL
                               includingPropertiesForKeys:@[]
                                                  options:NSDirectoryEnumerationSkipsHiddenFiles
                                                    error:nil];

for (NSString *filePath in contents) {
    [fileManager removeItemAtPath:filePath error:nil];
}

#### CustomFileManagerDelegate.m

~~~{objective-c}
#pragma mark - NSFileManagerDelegate

- (BOOL)fileManager:(NSFileManager *)fileManager
shouldRemoveItemAtURL:(NSURL *)URL
{
    return ![[[URL lastPathComponent] pathExtension] isEqualToString:@"pdf"];
}
~~~

## Ubiquitous Storage

### Move Item to Ubiquitous Storage

~~~{objective-c}
dispatch_async(dispatch_get_global_queue(DISPATCH_QUEUE_PRIORITY_BACKGROUND, 0), ^{
    NSFileManager *fileManager = [[NSFileManager alloc] init];
    NSString *documentsPath = [NSSearchPathForDirectoriesInDomains(NSDocumentDirectory, NSUserDomainMask, YES) firstObject];
    NSURL *fileURL = [NSURL fileURLWithPath:[documentsPath stringByAppendingPathComponent:@"Document.pages"]];

    // Defaults to first listed in entitlements when `nil`; should replace with real identifier
    NSString *identifier = nil;

    NSURL *ubiquitousContainerURL = [fileManager URLForUbiquityContainerIdentifier:identifier];
    NSURL *ubiquitousFileURL = [ubiquitousContainerURL URLByAppendingPathComponent:@"Document.pages"];

    NSError *error = nil;
    BOOL success = [fileManager setUbiquitous:YES
                                    itemAtURL:fileURL
                               destinationURL:ubiquitousFileURL
                                        error:&error];
    if (!success) {
        NSLog(@"[Error] %@ (%@) (%@)", error, fileURL, ubiquitousFileURL);
    }
});
~~~

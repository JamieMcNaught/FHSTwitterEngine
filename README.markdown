FHSTwitterEngine
================

***The synchronous Twitter engine that doesn't suck!!***

Created by [Nathaniel Symer](mailto:nate@natesymer.com), aka [@natesymer](http://twitter.com/natesymer) 

Feel free to <a href="http://natesymer.com/donate/" alt="Buy me a coffee or graphics card">buy me a coffee</a> (donation).


`FHSTwitterEngine` can:

- Login through xAuth.
- Login through oAuth. Login UI based on [SA_OAuthTwitterEngineController](https://github.com/bengottlieb/Twitter-OAuth-iPhone)
- Make a request to every available API endpoints. Yes, even the legal ones.


Why `FHSTwitterEngine` is better than `MGTwitterEngine`:

- Lack of annoying delegates
- Does not send you to Dependency Hell over a JSON parser
- Less setup
- Synchronous allowing for easier implementation (see Usage)
- More implemented API endpoints
- Uses a fixed version of OAuthConsumer (mine)
- **Less crufty**


**Setup**

1. Add the folder "FHSTwitterEngine" to your project and `#import "FHSTwitterEngine.h"` (I recommend `Prefix.pch`)
2. Link against `SystemConfiguration.framework`
3. In **Compile Sources** in the **Build Phases** tab of your target (e.g. MyCoolApp.app with the target icon next to it), add the `-fno-objc-arc` compiler flag to all files starting with `OA` (In other words, the `OAuthConsumer` library, *nothing* else)
4. Profit!!!!

**Usage:**

--> Create `FHSTwitterEngine` object:

    FHSTwitterEngine *engine = [[FHSTwitterEngine alloc]initWithConsumerKey:@"<consumer_key>" andSecret:@"<consumer_secret>"];
    
    // or 
    
    // If you plan to set your keys on a per-request basis
    FHSTwitterEngine *engine = [[FHSTwitterEngine alloc]init]; 
    
--> Login via OAuth:
    
    [self.engine showOAuthLoginControllerFromViewController:self withCompletion:^(BOOL success) {
        
        if (success) {
            NSLog(@"L0L success");
        } else {
            NSLog(@"O noes!!! Loggen faylur!!!");
        }
       
    }];
    
--> Login via XAuth:
    
    dispatch_async(GCDBackgroundThread, ^{
    	@autoreleasepool {
    		NSError *error = [engine getXAuthAccessTokenForUsername:@"<username>" password:@"<password>"];
        	// Handle error
        	dispatch_sync(GCDMainThread, ^{
    			@autoreleasepool {
        			// Update UI
        		}
       		});
    	}
    });
    
--> Set up your consumer manually and temorarily
	
	// your keys will be cleared after the next request is prepared, before it is sent.
	[engine temporarilySetConsumerKey:@"<consumer_key>" andSecret:@"<consumer_secret>"];
	
	// if you are really paranoid, use this
	[engine clearConsumer];
	
    
--> Reload a saved access_token:

    [engine loadAccessToken];

--> End a session:

    [engine clearAccessToken];

--> Check if a session is valid:

    [engine isAuthorized];
    
--> Do an API call (POST):

    dispatch_async(GCDBackgroundThread, ^{
    	@autoreleasepool {
    		NSError *error = [engine twitterAPIMethod]; 
    		// Handle error
    		dispatch_sync(GCDMainThread, ^{
    			@autoreleasepool {
        			// Update UI
        		}
       		});
    	}
    });

--> Do an API call (GET):

    dispatch_async(GCDBackgroundThread, ^{
    	@autoreleasepool {
    		id twitterData = [engine twitterAPIMethod];
    		// Handle twitterData (see "About GET REquests")
    		dispatch_sync(GCDMainThread, ^{
    			@autoreleasepool {
        			// Update UI
        		}
       		});
    	}
    });

**Grand Central Dispatch**

So what are those `GCDBackgroundThread` and `GCDMainThread` defines?<br />
They are macros for `dispatch_async()` and `dispatch_sync()`, respectively. They make using GCD much easier. Yes, GCD is the best way to make FHSTwitterEngine asynchonous. GCD allows for FHSTwitterEngine to remain procedural, but you knew that.

**General Comments**

`FHSTwitterEngine` will attempt to preëmtively detect errors in your requests. This is designed to prevent flawed requests from being needlessly sent.

**About POST Requests**

All methods that send POST requests, including the xAuth login method, return `NSError`. If there is no error, they should return `nil`.

**About GET requests**

GET methods return id. There returned object can be a member of one of the following classes:

- `NSDictionary`
- `NSArray`
- `UIImage`
- `NSString`
- `NSError`
- `nil`

In the case of `authenticatedUserIsBlocking:isID:` and `testService`, an NSString will be returned. It will be `@"YES"` to indicate YES and `@"NO"` to indicate NO. Additionally, it will return an `NSError` if it fails. How else could I prevent false negatives?

**For the future/To Do**

Feel free to [email](mailto:nate@natesymer.com) me for suggestions.

- Mac support
- Add custom objects for profile settings

**IMPORTANT**

`FHSTwitterEngine` contains an overhauled version of OAuthConsumer. The changes are:
- Removed `OADataFetcher`
- `OAAsynchronousDataFetcher` now contains one method that takes arguments of a request and a block.
- Fixed string comparisons
- Fixed memory leaks
- Fixed bugs
- Added compatibility with alternative versions of OAuthConsumer

**I'm from New Jersey, so pardon my sarcastic comments, mkay?**

**Fixes for some common problems** (and best practices)

- If you have any errors concerning multiple declarations for any class, check to make sure that any class is not importing another class which is importing the first class (aka `#import` loop - A imports B which imports A which imports B...)

kthxbye



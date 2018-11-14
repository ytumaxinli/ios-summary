```
/* When you elect to position the view using auto layout by adding your own constraints,
you must set this property to NO. IB will do this for you.*/
@property(nonatomic) BOOL translatesAutoresizingMaskIntoConstraints NS_AVAILABLE_IOS(6_0); // Default YES

// default is YES. if set, subviews are adjusted according to their autoresizingMask if self.bounds changes
@property(nonatomic)BOOL autoresizesSubviews; 

// simple resize. default is UIViewAutoresizingNone
@property(nonatomic)UIViewAutoresizingautoresizingMask; 
```

```

@property(nonatomic) BOOL translatesAutoresizingMaskIntoConstraints;
```



  



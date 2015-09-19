#Introduction to Smartypants rules

# Introduction #

The Smartypants IOC system uses injector rules when deciding how to fulfil a request.

# Details #

Each rule essentially matches a key to an instance (either an implementation, or a provider, or another rule). A key consists of a Class (or interface) and an optional name. An example would be (IUIComponent _named_ "BlueButton").

Adding a new rule is easy:

## Class Binding ##

```
//Get our injector
injector = SmartyPants.getOrCreateInjectorFor(this);
//Add some rules
injector.newRule().whenAskedFor(IUIComponent).named("BlueButton").useClass(Button);
injector.newRule().whenAskedFor(IUIComponent).useClass(Canvas);
```

This example demonstrates simple class binding, with the following meanings:
  * When injecting a IUIComponent named "BlueButton", provide an instance of Button.
  * When injecting an unnamed IUIComponent, provide an instance of Canvas.

## Instance Binding (singletons) ##

```
injector.newRule().whenAskedFor(String).named("wsdl").useInstance("http://www.server.com/soap/service.wsdl");
injector.newRule().whenAskedFor(IService).useSingletonOf(com.company.ServiceImpl);
```

In this example, we're binding:
  * The instance "http://www.server.com/soap/service.wsdl" to any requests for a String named "wsdl".
  * A "lazy singleton" of class ServiceImpl to any requests for an IService.

Lazy singletons (**useSingletonOf**) are not created until they're needed, whereas **useInstance** binds to an existing instance.

## Provider Binding ##

```
injector.newRule().whenAskedFor(Button).useProvider(myProviderInstance);
```

Binds to an instance of a class that implements to **Provider** interface.

## Rule Binding ##

```
injector.newRule().whenAskedFor(IUIComponent).named("RedButton").useRuleFor(IUIComponent, "BlueButton");
```

This code redirects all requests for (IUIComponent named "RedButton") to whatever is bound to (IUIComponent named "BlueButton")

## Live rules ##

Live rules in Smartypants are the IOC equivalent to the Flex SDK's data-binding functionality. For example:

```
[Bindable]
public var companyList : ArrayCollection;

injector.newRule().whenAskedFor(ArrayCollection).named("allCompanies").useBindableProperty(this, "companyList");
```

This, combined with **live injection** (see the request documentation) allows you to make use Flex databinding between model and view classes without any direct coupling between them.
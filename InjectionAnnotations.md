#Details of the annotations supported by the Smartypants IOC framework

# Introduction #

Just as the rules are the "supply" and "wiring" side of Smartypants, the requests and annotations are how objects specify their required dependencies.

# Annotations #

Smartypants makes use of custom annotations inside your Flex classes to instruct the injector as to what your class requires:

```
package mypackage // "Hey, check out..."
{
    import mx.rpc.soap.WebService;
    
    import net.expantra.smartypants.Provider;
    
    public class MyClass
    {
        
        [Inject]
        public var myWebService : WebService; // #1
        
        [Inject(name="wsdl")]
        public var wsdlLocation : String; // #2
        
        [Inject(type="mx.components.IUIComponent",name="BlueButton")]
        public var buttonProvider : Provider; // #3
        
        [Inject(name="currentCompany",live)]
        [Bindable]
        public var currentCompany : CompanyVO; // #4
    }
}
```

Above we see examples of the four basic request annotations.
  1. A simple request to inject a WebService. If there is no specific rule for class "WebService", the injector will simply instantiate a new one.
  1. Will inject the correct value for (String named "wsdl")
  1. Will inject a provider for (IUIComponent named "BlueButton") instead of an instance. This way you can call `buttonProvider.getInstance()` whenever you need a reference.
  1. This is a **live injection**, the IOC equivalent of Flex's data-binding. If (CompanyVO named "currentCompany") is bound to a bindableProperty, **this.currentCompany** will get updated every time the source changes. If it's a normal binding, it will get updated every time the binding rule is changed. This allows you to live-inject something that currently has no rule, and you'll get no errors at run-time, but the value will be provided as soon as Smartypants knows where to find it.
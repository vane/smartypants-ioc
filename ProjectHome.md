# SmartyPants IOC #

## Welcome ##

Welcome to the main repository for SmartyPants IOC, which is a [dependency injection](http://en.wikipedia.org/wiki/Dependency_injection) framework designed for use with [Adobe Flex](http://www.adobe.com/products/flex/).

## Why SmartyPants? ##

There are a few IOC frameworks around at the moment: [Prana (now Spring Actionscript)](http://www.springactionscript.org/) was probably the first serious DI framework for Flex to get much notice. There's also [Swiz](http://code.google.com/p/swizframework/), and others. What sets Smartypants apart from these frameworks?

  * The competition is (mostly) inspired by [Spring](http://www.springframework.org/about), whereas SmartyPants is inspired by [Guice](http://code.google.com/p/google-guice/). This is simply my preference, it may or may not be yours.
  * SmartyPants-IOC is _just_ for dependency injection. You may use any application framework you please. I recommend [RobotLegs AS3](http://robotlegs.org/). It does things the way I like, and you can even replace SmartyPants with another injector if you need to.
  * SmartyPants defines the bindings / application structure via [ActionScript code](Rules.md), not via XML. Again, this is a stylistic preference. **Put your run-time configuration in configuration files, _not_ in your application structure**.
  * Injection requests are specified with [annotations](InjectionAnnotations.md), like Guice, Spring 2(.5?), and Swiz. You can also use a [similar DSL](RequestDSL.md) to request instances from the injector.
  * SmartyPants provides [live injection](LiveInjection.md) - think "the love child of regular dependency injection and Flex data-binding"

## Getting started ##

Download the .swc file from here, or checkout the code via SVN. The project is young, and the docs are growing, but grow they will! Check out the wiki here, or join [the discussion group](http://groups.google.com/group/smartypants-ioc) if you'd like to know more. The !ASDocs are currently offline but will return very shortly!

## A simple example ##

```
<?xml version="1.0" encoding="utf-8"?>
<mx:Application xmlns:mx="http://www.adobe.com/2006/mxml" layout="absolute" preinitialize="setupInjector()" viewSourceURL="srcview/index.html">

    <mx:Script>
        <![CDATA[
            import mx.controls.Alert;
            import net.expantra.smartypants.SmartyPants;
            import net.expantra.smartypants.Injector;

            private var injector : Injector;

            [Inject(name="wsdl")]
            [Bindable]
            public var wsdlLocation : String;

            [Inject(live, name="liveExample")]
            [Bindable]
            public var textBox2Value : String = "Not injected";

            private function setupInjector() : void
            {
                injector = SmartyPants.getOrCreateInjectorFor(this);
            }

            private function setUpRules() : void
            {
                injector.newRule().whenAskedFor(String).named("wsdl").useInstance("http://www.server.com/service/main.wsdl");
                injector.newRule().whenAskedFor(String).named("liveExample").useBindableProperty(this, ["tb1", "text"]);
            }

            private function inject() : void
            {
                try
                {
                    setUpRules();
                    injector.injectInto(this);
                }
                catch (e : Error)
                {
                    trace(e.getStackTrace());
                    Alert.show("Oops! " + e, "Error performing injection");
                }
            }

        ]]>
    </mx:Script>

    <mx:Panel title="Smartypants IOC demo" horizontalCenter="0" verticalCenter="0">

        <mx:Form>

            <mx:FormItem label="WSDL location">
                <mx:TextInput text="{ wsdlLocation }" editable="false"/>
            </mx:FormItem>

            <mx:FormItem label="Source Textbox">
                <mx:TextInput id="tb1"/>
            </mx:FormItem>

            <mx:FormItem label="Destination Textbox">
                <mx:TextInput id="tb2" text="{ textBox2Value }"/>
            </mx:FormItem>

            <mx:HBox width="100%" horizontalAlign="right">
                <mx:Button label="Inject into this" click="inject()"/>
            </mx:HBox>

        </mx:Form>

    </mx:Panel>

</mx:Application>
```
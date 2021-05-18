# juce_browser_integration

juce_browser_integration is a JUCE module providing helper classes to make it easy to integrate a web-based UI (running in a JUCE WebBrowserComponent) with a JUCE application (e.g. an audio engine).

**Please see https://github.com/tomduncalf/WebUISynth for an example integration with a simple synth engine.**

Ultimately the integration will be cross-platform on both desktop and mobile, but right now it has only been tested on macOS and iOS (where it currently works standalone but not as an AUv3).

## Status

Work in progress - I have a [basic example](https://github.com/tomduncalf/WebUISynth) working, but need to add more features, documentation and a tutorial. Should not be considered stable in terms of APIs etc. See [TODO](#TODO).

## Features

- A [browser component](https://github.com/tomduncalf/tomduncalf_juce_browser_integration/blob/main/BrowserComponent.h) which supports sending messages to and from a web browser, using a custom `juce://` URL schema (eventually it would be nice to use direct callbacks for potential performance improvements, but this requires JUCE changes to support)

- A [base class](https://github.com/tomduncalf/tomduncalf_juce_browser_integration/blob/main/BrowserIntegrationClient.h) for any classes which want to communicate with the browser, allowing sending messages and registering callbacks in a namespaced way.

- Automatic one-way state synchronisation from JUCE ValueTrees to a JS representation using a [ValueTreeSynchroniser](https://github.com/tomduncalf/tomduncalf_juce_browser_integration/blob/main/BrowserValueTreeSynchroniser.h) and a [Typescript implementation](https://github.com/tomduncalf/WebUISynth/tree/main/ui/src/juceIntegration/valueTree) of the ValueTreeSynchroniser protocol.

- A [base class](https://github.com/tomduncalf/tomduncalf_juce_browser_integration/blob/main/BrowserIntegrationPluginClient.h) for plugins which automatically handles synchonising the plugins AudioProcessorValueTreeState to the browser, and a (corresponding class) on the JS side which provides a representation of the parameters with getters and setters. [Auto-generated Typescript definitions](https://github.com/tomduncalf/WebUISynth/blob/main/ui/src/config/autogenerated/parameters.ts) for your parameters (when run in debug mode) reduces the amount of manual integration to a minimum.

- "Batteries included" defaults, using [MobX](https://mobx.js.org/) and [React](https://reactjs.org/) to make working with your audio engine as straightforward as possible – for example, Parameters are exposed via React Context, making hooking up your parameters [easy](https://github.com/tomduncalf/WebUISynth/blob/main/ui/src/components/Parameters.tsx).

- Good performance – in my initial tests, a web based UI seemed to perform similarly to a native JUCE UI in terms of CPU usage when playing back a sequence in a host with automation and a scope visualiser open. This area needs more research however!

- Cross-platform (planned, currently Mac/iOS only) – every platform supported by JUCE now provides a mature WebView component, so web UIs should perform well across all platforms, both standalone and as a hosted plugin.

- Great developer experience – integration is made as straightforward as possible on both the JUCE and browser sides, and features web developers love such as hot reloading and developer tools are all available. As the UI is "just a web page", your favourite libraries can be reused for functionality and styling too.

## Documentation

Unforuntately documentation is still TODO – once I am happy with the APIs and functionality, I intend to write up documentation and a tutorial but for now, there are some code comments in the source code and the example [WebUISynth](https://github.com/tomduncalf/WebUISynth) integration.

## Motivation

Building user interfaces in C++ can be challenging, especially for developers coming from a web background. Advances in the web world over the last decade including declarative UI frameworks such as [React](https://reactjs.org/), hot reloading, and the ever expanding capabilities of both CSS and Javascript have made developing complex UIs considerably easier – going back to imperative code updating a UI and a full compile cycle each time you make a change can feel like quite a step backwards.

If you are happy to build for just one platform, native frameworks offer a much better experience, but if you are building a cross-platform app, options are more limited. I have worked extensively with React Native in combination with JUCE (see my [talk from ADC'19](https://www.youtube.com/watch?v=bsy0-mHcS4Y) about building an app with this stack), but on desktop React Native is still quite an immature option.

Flutter is an interesting option in many ways, but it requires learning a new framework and language, whereas Javascript (and in particular React) is widely known and fairly easy to learn. Browser engines are incredibly powerful and remarkably performant, so in theory there's no reason why they shouldn't be suitable for building UIs for audio apps and plugins – indeed, a developer from Output spoke about their experiences building their Arcade plugin using web UI at [ADC'20](https://www.youtube.com/watch?v=XvsCaQd2VFE]).

The difficulty often comes with combining and interacting between two separate worlds – you have to worry about keeping state in sync, how to pass messages between JS and C++, etc. This module aims to solve those problems as elegantly as possible, so you can focus on writing great software.

## TODO

- [ ] Documentation
- [ ] Tutorial
- [ ] Modularise supporting Typescript code into an npm package
- [ ] Send begin/end paramter change gesture messages
- [ ] Investigate using browser callbacks to pass messages rather than custom URL schema (will require JUCE changes)
- [ ] Windows/Linux support
- [ ] Use the parameter metadata sent across from JUCE to set min, max, etc. on components
- [ ] Proper typing of "choice" components
- [ ] Allow the web UI to be run in the browser for easier development (maybe using a snapshot of the parameter value tree?)
- [ ] iOS AUv3 support (right now the browser won't load the UI, hopefully just needs some security entitlements)
- [ ] Lots more!
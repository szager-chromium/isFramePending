# navigator.scheduling.isFramePending

## Author
szager@chromium.org

## Introduction 
The purpose of this API is to allow developers to make informed decisions about when to yield from javascript code, in order to give the rendering pipeline an opportunity to run (and thereby avoid visual jank). It provides insight into whether the browser needs to refresh the visual display by running its rendering steps.

## Problem Statement 
Browsers cannot update the rendering of a page while javascript is running; those tasks must run serially (as described in the [HTML processing model](https://html.spec.whatwg.org/multipage/webappapis.html#event-loop-processing-model)). If an uninterruptible javascript task runs too long, it may cause the browser to fall behind in updating the rendering, resulting in dropped or missed animation frames ("jank").

To ensure smooth animations and visual updates, web developers must schedule their application logic to run without delaying necessary rendering updates ([more info](https://developers.google.com/web/fundamentals/performance/rendering/optimize-javascript-execution)). Presently, the only real strategy for doing this is to break up application logic into small chunks and yield control to the browser frequently. This can be quite limiting for developers that want to write scheduling logic into their javascript code and yield to the browser less frequently: doing so increases the likelihood that the browser will fall behind in updating the rendering. The web platform doesn't provide any signal that rendering is falling behind, or that the browser needs to update the rendering.

## Proposed Solution
isFramePending returns a boolean indicating whether any [browsing contexts](https://html.spec.whatwg.org/multipage/browsers.html#concept-document-bc) currently have a [rendering opportunity](https://html.spec.whatwg.org/multipage/webappapis.html#rendering-opportunity). If the return value is `true`, the browser will run the "Update the Rendering" steps on the next iteration of the event loop, as specified in step 11 of the HTML [processing model](https://html.spec.whatwg.org/multipage/webappapis.html#event-loop-processing-model).

Here's how the API might be used by an application that has a main loop in javascript:

```javascript
function mainLoop() {
  // As long as there is work to do, don't yield back to the browser until it
  // needs to update the rendering.
  while (thereIsWorkToDo() && !navigator.scheduling.isFramePending()) {
    getNextWorkItem().run();
  }

  // If there is more work to do, but you want to give the browser a chance to
  // update the rendering, re-schedule the main loop to run again later.
  if (thereIsWorkToDo()) {
    setTimeout(mainLoop, 0);
  }
};

button.addEventListener('click', e => {
  let workItem = createWorkItemToHandleEvent(e);
  if (isUrgent(e)) {
    // Don't yield to the browser, even at risk of delaying rendering.
    workItem.run();
  } else {
    // Let the mainLoop logic decide whether to run the event handler code
    // immediately or postpone it until later.
    enqueueWorkItem(workItem);
    mainLoop();
  }
});
```

One limitation of the above code is that event listeners might be delayed while `mainLoop` runs through all pending work. Another proposed API, [navigator.scheduling.isInputPending](https://github.com/WICG/is-input-pending), can be included to make the app more responsive:

```javascript
function mainLoop() {
  mainLoopCallbackId = 0;
  while (thereIsWorkToDo() && !navigator.scheduling.isFramePending() &&
         !navigator.scheduling.isInputPending()) {
    doOneUnitOfWork();
  }

```

## Design Decisions and Non-Goals

This API is not intended to be predictive in any way -- it won't tell you if a rendering opportunity will happen "soon", or provide any budget or estimated time until the next rendering opportunity. It only indicates whether the browser has *already* identified a rendering opportunity that it is waiting to service.

Originally, a `scheduling.timeUntilNextFrame()` API was considered, but it was concluded that this information was impossible to determine accurately in some important scenarios.

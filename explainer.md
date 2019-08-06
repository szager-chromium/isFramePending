# Window.isFramePending

Window.isFramePending returns a boolean indicating whether any [browsing contexts](https://html.spec.whatwg.org/multipage/browsers.html#concept-document-bc) currently have a [rendering opportunity](https://html.spec.whatwg.org/multipage/webappapis.html#rendering-opportunity). If the return value is `true`, the browser will run the "Update the Rendering" steps on the next iteration of the event loop, as specified in step 11 of the HTML [processing model](https://html.spec.whatwg.org/multipage/webappapis.html#event-loop-processing-model).

The purpose of this API is to allow developers to make informed decisions about when to yield from javascript code, in order to give the rendering pipeline an opportunity to run (and thereby avoid visual jank).

# Luigi Client API

<!-- Generated by documentation.js. Update this documentation by updating the source code. -->

## LuigiClient

### linkManager

The Link Manager allows you to navigate to another route. Use it instead of an internal router to:

-   Route inside micro front-ends.
-   Reflect the route.
-   Keep the navigation state in Luigi.

### uxManager

Use the UX Manager to manage the appearance features in Luigi.

## lifecycle

Use the functions and parameters to define the lifecycle of listeners, navigation nodes, and Event data.

### addInitListener

Registers a listener called with the context object as soon as Luigi is instantiated. Defer your application bootstrap if you depend on authentication data coming from Luigi.

#### Parameters

-   `initFn` **[function](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Statements/function)** the function that is called once Luigi is initialized

### removeInitListener

Removes an init listener.

#### Parameters

-   `id` **[string](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/String)** the id that was returned by the `addInitListener` function

### addContextUpdateListener

Registers a listener called with the context object upon any navigation change.

#### Parameters

-   `contextUpdatedFn` **[function](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Statements/function)** the listener function called each time Luigi context changes

### removeContextUpdateListener

Removes a context update listener.

#### Parameters

-   `id` **[string](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/String)** the id that was returned by the `addContextUpdateListener` function

### getEventData

Returns the context object. Typically it is not required as the [addContextUpdateListener()](#addContextUpdateListener) receives the same values.

Returns **[Object](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object)** current context data

### getNodeParams

Returns the node parameters of the active URL.
Node parameters are defined like URL query parameters but with a specific prefix allowing Luigi to pass them to the micro front-end view.  The default prefix is **~** and you can use it in the following way: `https://my.luigi.app/home/products?~sort=asc~page=3`.

> **NOTE:** some special characters (`<`, `>`, `"`, `'`, `/`) in node parameters are HTML-encoded.

Returns **[Object](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object)** node parameters, where the object property name is the node parameter name without the prefix, and its value is the value of the node parameter. For example `{sort: 'asc', page: 3}`

### getPathParams

Returns the dynamic path parameters of the active URL.
Path parameters are defined by navigation nodes with a dynamic **pathSegment** value starting with **:**, such as **productId**.
All path parameters in the current navigation path (as defined by the active URL) are returned.

> **NOTE:** some special characters (`<`, `>`, `"`, `'`, `/`) in path parameters are HTML-encoded.

Returns **[Object](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object)** path parameters, where the object property name is the path parameter name without the prefix, and its value is the actual value of the path parameter. For example `{productId: 1234, ...}`

## linkManager

### navigate

Navigates to the given path in the application hosted by Luigi. It contains either a full absolute path or a relative path without a leading slash that uses the active route as a base. This is the standard navigation.

#### Parameters

-   `path` **[string](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/String)** path to be navigated to
-   `sessionId` **[string](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/String)?** current Luigi **sessionId**
-   `preserveView` **[boolean](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Boolean)?** preserve a view by setting it to `true`. It keeps the current view opened in the background and opens the new route in a new frame. Use the [goBack()](#goBack) function to navigate back. You can use this feature across different levels. Preserved views are discarded as soon as the standard [navigate()](#navigate) function is used instead of [goBack()](#goBack)
-   `modalSettings` **[Object](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object)?** opens a view in a modal. Use these settings to configure the modal's title and size
    -   `modalSettings.title` **[string](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/String)** modal title. By default, it is the node label. If there is no label, it is left empty
    -   `modalSettings.size` **(`"l"` \| `"m"` \| `"s"`)** size of the modal (optional, default `"l"`)

#### Examples

```javascript
LuigiClient.linkManager().navigate('/overview')
LuigiClient.linkManager().navigate('users/groups/stakeholders')
LuigiClient.linkManager().navigate('/settings', null, true) // preserve view
```

### openAsModal

Opens a view in a modal. You can specify the modal's title and size. If you don't specify the title, it is the node label. If there is no node label, the title remains empty.  The default size of the modal is `l`, which means 80%. You can also use `m` (60%) and `s` (40%) to set the modal size. Optionally, use it in combination with any of the navigation functions.

#### Parameters

-   `path` **[string](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/String)** navigation path
-   `modalSettings` **[Object](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object)?** opens a view in a modal. Use these settings to configure the modal's title and size
    -   `modalSettings.title` **[string](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/String)** modal title. By default, it is the node label. If there is no label, it is left empty
    -   `modalSettings.size` **(`"l"` \| `"m"` \| `"s"`)** size of the modal (optional, default `"l"`)

#### Examples

```javascript
LuigiClient.linkManager().openAsModal('projects/pr1/users', {title:'Users', size:'m'});
```

### fromContext

Sets the current navigation context to that of a specific parent node which has the [navigationContext](navigation-configuration.md) field declared in the navigation configuration. This navigation context is then used by the `navigate` function.

#### Parameters

-   `navigationContext` **[string](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/String)** 

#### Examples

```javascript
LuigiClient.linkManager().fromContext('project').navigate('/settings')
```

Returns **[linkManager](#linkmanager)** link manager instance

### fromClosestContext

Sets the current navigation context which is then used by the `navigate` function. This has to be a parent navigation context, it is not possible to use the child navigation contexts.

#### Examples

```javascript
LuigiClient.linkManager().fromClosestContext().navigate('/users/groups/stakeholders')
```

Returns **[linkManager](#linkmanager)** link manager instance

### withParams

Sends node parameters to the route. The parameters are used by the `navigate` function. Use it optionally in combination with any of the navigation functions and receive it as part of the context object in Luigi Client.

#### Parameters

-   `nodeParams` **[Object](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object)** 

#### Examples

```javascript
LuigiClient.linkManager.withParams({foo: "bar"}).navigate("path")

// Can be chained with context setting functions such as:
LuigiClient.linkManager.fromContext("currentTeam").withParams({foo: "bar"}).navigate("path")
```

Returns **[linkManager](#linkmanager)** link manager instance

### pathExists

Checks if the path you can navigate to exists in the main application. For example, you can use this helper method conditionally to display a DOM element like a button.

#### Parameters

-   `path` **[string](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/String)** path which existence you want to check

#### Examples

```javascript
let pathExists;
 this.luigiClient
 .linkManager()
 .pathExists('projects/pr2')
 .then(
   (pathExists) => {  }
 );
```

Returns **[promise](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise)** a promise which resolves to a Boolean variable specifying whether the path exists or not

### hasBack

Checks if there is one or more preserved views. You can use it to show a **back** button.

Returns **[boolean](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Boolean)** indicating if there is a preserved view you can return to

### goBack

Discards the active view and navigates back to the last visited view (preserved view), if a preserved view was set before.

#### Parameters

-   `goBackValue` **any** data that is passed in the `goBackContext` field to the last visited view

#### Examples

```javascript
LuigiClient.linkManager().goBack({ foo: 'bar' });
LuigiClient.linkManager().goBack(true);
```

## uxManager

### showLoadingIndicator

Adds a backdrop with a loading indicator for the micro front-end frame. This overrides the [loadingIndicator.enabled](navigation-configuration.md#nodes) setting.

### hideLoadingIndicator

Removes the loading indicator. Use it after calling [showLoadingIndicator()](#showLoadingIndicator) or to hide the indicator when you use the [loadingIndicator.hideAutomatically: false](navigation-configuration.md#nodes) node configuration.

### addBackdrop

Adds a backdrop to block the top and side navigation. It is based on the Fundamental UI Modal, which you can use in your micro front-end to achieve the same behavior.

### removeBackdrop

Removes the backdrop.

### setDirtyStatus

This method informs the main application that there are unsaved changes in the current view in the iframe. For example, that can be a view with form fields which were edited but not submitted.

#### Parameters

-   `isDirty` **[boolean](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Boolean)** indicates if there are any unsaved changes on the current page or in the component

### showConfirmationModal

Shows a confirmation modal.

#### Parameters

-   `settings` **[Object](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object)?** the settings the confirmation modal. If no value is provided for any of the fields, a default value is set for it
    -   `settings.header` **[string](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/String)** the content of the modal header (optional, default `"Confirmation"`)
    -   `settings.body` **[string](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/String)** the content of the modal body (optional, default `"Are you sure you want to do this?"`)
    -   `settings.buttonConfirm` **[string](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/String)** the label for the modal confirm button (optional, default `"Yes"`)
    -   `settings.buttonDismiss` **[string](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/String)** the label for the modal dismiss button (optional, default `"No"`)

Returns **[promise](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise)** which is resolved when accepting the confirmation modal and rejected when dismissing it

### showAlert

Shows an alert.

#### Parameters

-   `settings` **[Object](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object)** the settings for the alert
    -   `settings.text` **[string](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/String)** the content of the alert. To add a link to the content, you have to set up the link in the `links` object. The key(s) in the `links` object must be used in the text to reference the links, wrapped in curly brackets with no spaces. If you don't specify any text, the alert is not displayed
    -   `settings.type` **(`"info"` \| `"success"` \| `"warning"` \| `"error"`)?** sets the type of the alert
    -   `settings.links` **[Object](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object)?** provides links data
        -   `settings.links.LINK_KEY` **[Object](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object)** object containing the data for a particular link. To properly render the link in the alert message refer to the description of the **settings.text** parameter
            -   `settings.links.LINK_KEY.text` **[string](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/String)** text which replaces the link identifier in the alert content
            -   `settings.links.LINK_KEY.url` **[string](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/String)** url to navigate when you click the link. Currently, only internal links are supported in the form of relative or absolute paths

#### Examples

```javascript
import LuigiClient from '@kyma-project/luigi-client';
const settings = {
 text: Ut enim ad minim veniam, {goToHome} quis nostrud exercitation ullamco {relativePath} laboris nisi ut aliquip ex ea commodo consequat.
   Duis aute irure dolor {goToOtherProject},
 type: 'info',
 links: {
   goToHome: { text: 'homepage', url: '/overview' },
   goToOtherProject: { text: 'other project', url: '/projects/pr2' },
   relativePath: { text: 'relative hide side nav', url: 'hideSideNav' }
 }
}
LuigiClient
 .uxManager()
 .showAlert(settings)
 .then(() => {
    // Logic to execute when the alert is dismissed
});
```

Returns **[promise](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise)** which is resolved when the alert is dismissed

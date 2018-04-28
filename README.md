# eform-v5-migration

> Migration guide and tooling for eForm v4 to v5.

## Preface

### Important decision regarding eForm version 5.0

While creating a robust form engine, we have found that our current approach for creating and managing form data, does not scale well with today's necessities. The data that is stored in the database through PHP doesn't communicate well with javascript (for various reasons and differences of the languages). This is the main reason, why up until now, we couldn't focus on creating a scalable and live form builder.

In the past couple of months, we have been exploring how we can create a form engine, that is simple enough to use and robust enough to scale. After much research, we have come to a conclusion that:

1. We should use WordPress API and PHP to store form and submission data.
2. Use JavaScript and libraries like React to render the form to the user and admin.
3. But this approach has one major drawback. We can not seamlessly upgrade eForm v4 data to v5. If we try to do that, the huge differences of the frameworks would simply burn our effort.
4. eForm Version 5 will not be backward compatible
5. After much thinking and reviewing our codebase, we have found that, we can not use the legacy approach to write a modern and scalable form engine. We are relying heavily on some language specific behavior of PHP to store and retrieve form data and we just can not pass it down to javascript without some unknown and unwanted behavioral bugs which most probably will make the form engine unusable.

Instead, we are taking a new approach.

1. Use JSON like data structure to store and retrieve form data from database.
2. Use PHP to create factories of elements which can easily be scaled and modified without affecting other parts of the code. Right now, elements and form logic are inter-mingled.
3. Use MVC pattern to manage form data and control specific code within PHP. This will make unit and integration testing super easy and actually possible.
4. Once we pass the form data from PHP (server) to JavaScript (client), we will use a powerful library React to render the form.
5. All submission data will be stored in a flux like store (we will use Redux).
6. Once the data goes to the server, some Model like PHP classes will be used to manipulate it and call your integrations (through another integrations factory which scales on its own).

The benefits of this approach are many.

1. For starter, we don't have to rely on PHP to render the form which right now causes a full server re-render when we want to update any form component (like opening the form preview in a separate tab and then keep on refreshing it after adding new elements).
2. We can separate the application logic and modeling from various other parts of the form engine. Right now, for example, the logic of not showing the form when user is not logged in, is hard-coded within the form view itself. When eForm was small, it wasn't really a problem to maintain, but now we have crossed a milestone when we are struggling to keep up with the development.
3. We can store any form data or submission data to any type of storage (like local storage or server-side WordPress database) and can boot it up from there without any side-effects.
4. We are taking form styling a step further where the core CSS is stored in static files and style specifics are stored in the powerful cssinjs framework. So when creating new styles for your form, you will see it just live, without any page reload.
5. While using libraries like React, React DOM we are forced to use bundlers (we are using webpack) and therefore the load time will be dramatically decreased and we won't actually be relying on any global scopes of any javascript framework. So yes, goodbye to jQuery, jQuery UI and all the breaking things that come with it when some plugin or theme developer does it really wrong.
6. In time, we can even support AJAX type themes where pages are hot-loaded.
And there are many more.

### Why backward incompatibility
As we have found and settled on our new approach, we have found that we need to rewrite about 70% of the PHP logic, ditch all the views written in PHP and create React Components for the same.

This is not going to be an easy journey and on top of it if we try to somehow "hackishly" make old data work with the new system, many new bugs will inevitably arrive and we will suffer huge burn out. At this stage, it makes a lot more sense to start fresh, and re-use only individual modules that we have created since past couple of years (like integrations and payments).

With eForm v5, we will create new database tables, with new names, have individual roles and capabilities built-in and rewrite all of PHP code with namespaces to avoid any conflict.

### For eForm v4 users
We understand that you are currently using eForm version 4 and have created a lot of forms with it. Although eForm v5 can run parallel with eForm v4, we will not ditch eForm v4 right away. Instead we have the following plans.

1. Mark eForm v4 in a freeze mode. So we won't be adding any new features to the codebase.
2. We will support v4 for next 6 months after we launch v5.
3. As you have eForm v4 installed, you will continue to receive automatic updates for any patches or bug fixes. But you will not receive automatic update to v5. If you want to do that, you have to install v5 manually. After that, you will start receiving updates for v5.
4. In time, we will provide tooling to convert v4 forms to v5. If the demand is high enough, we will also consider creating a tool to migrate submission data from v4 to v5.

I hope you understand why we take this drastic decision. Frankly, if we do not do this now, eForm will eventually die and of course we don't want that to happen.

And as for any features or integrations, be assured that v5 will offer same, if not more, functionalities as v4.

Also do note that the update to v5 will be **free**. We will not charge existing customers for the update.

To show us your concerns or ideas, feel free to create issues in this repository.

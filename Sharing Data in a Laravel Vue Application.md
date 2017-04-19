WRITTEN BY
Jesse Schutt 	
POSTED ON
March 23rd, 2017  
VIA
https://zaengle.com/blog/layers-of-a-laravel-vue-application

When I first began kicking the tires on Vue, I was quickly frustrated by my lack of understanding of how data is transferred between Laravel and Vue. I was comfortable with the way Laravel retrieved and stored data, but I couldn't figure out how to make Vue aware of Laravel's data!

Getting Laravel and Vue to talk to each other has always been confusing for me. However, once I began thinking of applications in terms of "layers" it became much more clear.

## The Source of Truth

In most applications, the database is considered the "source of truth" upon which other parts of the application are built. Because of this, it's helpful to have a few strategies for pulling data out of and pushing data back into the database.

Laravel's Eloquent is a seamless solution for interacting with a number of different database types, such as the ever-standard MySQL, Postgres, or SQLite. It's simple to gather bits of information from the database, assemble it into the desired data structure, and pass it along to a Blade template for display in the browser.

However, things become more complicated when Vue needs to be aware of that same data. How does Vue get access to it? And once Vue has modified the data, how does the database get the changes?

This tripped me up for some time, but I've landed on a few strategies that have worked well in my applications.

## Thinking in Terms of "Layers"

First, it has been helpful to think of my application as "layers" or separate sections of code each with varying responsibilities. I like to think of Laravel as the intermediary between the persistent data in the database, and the Vue components comprising the front-end of the app.

Though the boundaries between Laravel and Vue may appear unclear at the outset, the only way one can know about the other's data is if it is explicitly passed. Blade templates implicitly know a lot about the application, but Vue operates on a different layer, so it only knows what we tell it. Fortunately, there are a number of ways to provide Vue with the data it needs.

## Data Transfer Strategies

### Using a Global Window Object

![Layers Settings Window](https://s3.amazonaws.com/zaengle//blog/layers-settings-window.jpg?mtime=20170322213846)

Occasionally I've pushed a JSON object into a script tag in my Blade templates and imported them into my Vue components. This is especially helpful for global data, like acquiring Laravel's CSRF token, or application-wide variables.

### Using Vue Props

Another option might look something like this: Laravel receives a request, retrieves information from the database, transforms it accordingly, returns a Blade template and passes the data as "props" into the Vue component.

![Layers Vue Props](https://s3.amazonaws.com/zaengle//blog/layers-vue-props.jpg?mtime=20170322213922)

This is a fairly linear approach and is most useful when Vue is primarily responsible for displaying information derived from the database. In other words, the Vue "layer" is receiving data from the Laravel "layer" and showing it to the user.

### Using Ajax / Axios / Vue-Resource

![Layers Vue Resource](https://s3.amazonaws.com/zaengle//blog/layers-vue-resource.jpg?mtime=20170322213924)

In this strategy, Laravel isn't responsible for passing information from its layer to the Vue layer via props, but instead, it simply sets up the wrapping Blade template containing Vue components. Once the components are instantiated they reach back to different Laravel end-points designed to return the data the component needs. One major benefit of this approach is these data endpoints can be reused throughout your application. (This is also a helpful approach to understand if you want to build single page applications.)

When Vue makes changes to the data or creates new data, it sends it back to the Laravel layer via something like Axios or Vue-Resource. At that point, Laravel is responsible for making the necessary changes and persisting them to the database.

One way or the other isn’t necessarily better; I choose depending on where I think the application is headed. If there is the possibility the application will need significant amounts of frontend JavaScript, then I will likely make the components retrieve their own data. But, if the app looks like it will remain fairly static then I will stick with simple Vue components and pass in data via props.

## Single Page Applications

The separation of "layers" is even more pronounced when building single page applications (SPA). If you are comfortable with building traditional Laravel applications with Blade and full page reloads, branching into SPAs requires a change of mindset. No longer do you have the luxury of retrieving fresh data on every page load. Now you must retrieve data at certain points, store it in the SPA, and push it back to Laravel.

![Layers Spa](https://s3.amazonaws.com/zaengle//blog/layers-spa.jpg?mtime=20170322213920)

In a SPA, there are essentially two sets of data the application runs on: the database and the local "store."

The primary data comes from the database and is used to construct the initial state, or store, of the SPA. This data may come from "props," data on the window, or by using an ajax method to ask Laravel.

This "store" is used for temporary data, and should be considered transient because once the SPA is closed, the data in a store disappears! This is why pushing data back to Laravel for persistent storage is necessary.

## Conclusion

Treating Laravel and Vue as uniquely separate "layers" of an application and understanding how to explicitly pass data between them allows us to harness the strengths of each tool.

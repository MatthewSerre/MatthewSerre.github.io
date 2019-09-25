---
layout: post
title:      "Using JavaScript with Rails"
date:       2019-09-25 02:08:32 +0000
permalink:  using_javascript_with_rails
---


I struggled at first with understanding how to bring everything I learned about JavaScript together with Rails to generate dynamic page content.  Of course Rails allows you to render pages dynamically through embedded Ruby files, but at the cost of constant page refreshes.  How does that work?

The end user interacts with your app through views and submits their requests to the controller, which delivers the requests to the models, retrieves the data, and delivers it back to the views and, thus, the end user.

For example, by visiting the caseload index page of my case management app, the user requests of the controller the data necessary to populate the view with a list of case managers and links to their caseload.

```
# /app/views/caseloads/index.html.erb

<ul class="collection">
<% @caseloads.each do |caseload| %>
    <li class="collection-item"><%= link_to "#{caseload.user.name}'s caseload", user_caseload_url(caseload.user, caseload) %></li>
<% end %>
</ul>
```

The view has access to an instance variable from the caseloads controller.


```
    # /app/controllers/caseloads_controller.rb

    def index
        @caseloads = Caseload.all
		end
```

This example is straightforward and requires little code, so I thought this was an efficient way of loading content for a web app; that is, until I started to grasp how to do the same thing with JavaScript.  The resources required for this example are small, but with a more complex app or view, sending a new get request and reloading an entire page repeatedly adds up.

With JavaScript, you can create a simple application program interface to obtain the same data from your models, allowing your users to stay on the same page while still receiving new data.

```
    # /app/controllers/caseloads_controller.rb

    def index
        @caseloads = Caseload.all
        respond_to do |f|
            f.html
            f.json { render json: @caseloads }
        end
    end
```

A minimal update to the index method allows us to render a page via HTML that relies on our index.html.erb file or obtain JavaScript Object Notation of the data so we can use JavaScript to supply the data to the existing HTML structure on the current page.

The next step is ensuring we are obtaining the exact JSON data we want, which is where the Active Model Serializers gem helps.  Installing the gem gives us access to a Rails generator that can create serializers for the models from which we want to request the data.

```
# /app/serializers/caseload_serializer.rb

class CaseloadSerializer < ActiveModel::Serializer
  attributes :id

  belongs_to :user
  has_many :clients
end
```

Serializer files are similar to their corresponding model files.  In this case, the caseload serializer has the same relationships as the caseload model, both of which belong to a user and have many clients.  Including the relationships in the serializer allows us to obtain via JSON information data about those models as well.  The attributes allow us to control which data we receive in the JSON format.  In my app, caseloads themselves only have IDs, but the important info is the user to which the caseload belongs and the clients it contains.  For example:

```
# /caseloads.json

[
{
id: 1,
user: {
id: 1,
first_name: "Matthew",
last_name: "Serre"
},
clients: [
{
id: 2,
first_name: "Billy",
last_name: "Bob"
},
{
id: 3,
first_name: "Samuel",
last_name: "Adams"
}
]
}
]
```

The next step is to obtain and process this JSON data in a way our app can use it.

```
// /app/assets/javascripts/caseloads.js

$(() => {
    bindClickHandlers()
})

const bindClickHandlers = () => {
    $('.all_caseloads').on('click', (e) => {
        e.preventDefault()
        fetch(`/caseloads.json`)
            .then((res) => res.json())
            .then(caseloads => {
...
```

The caseloads.js file, which should be precompiled in /config/initializers/assets.rb and included in /app/views/layouts/application.html.erb, includes code that changes the default behavior of the link to the caseloads index page.  Instead of sending a get request to the controller and rendering the index.html.erb file, the bindClickHandlers function hijacks the request by preventing the default action and fetching the data from /caseloads.json.  The returned promise and response object contain the data we can use to construct Caseload objects and format the current page to resemble the same index view we used to obtain from the aforementioned embedded Ruby file.

```
function Caseload(caseload) {
    this.id = caseload.id
    this.clients = caseload.clients
    this.user = caseload.user
}
```

In the same caseloads.js file, we can use a JavaScript constructor function to generate new Caseload objects using the JSON data much in the same way we might generate new instances in Ruby.  We can then write prototype methods to call on those objects.

```
Caseload.prototype.formatIndex = function() {
    let caseloadHtml = `
        <li><a href="/users/${this.user.id}/caseloads/${this.id}" data-caseload-id="${this.id}" data-user-id="${this.user.id}" class="show_link">${this.user.first_name} ${this.user.last_name}'s caseload</a></li>
    `
    return caseloadHtml
}
```

This formatIndex function dynamically generates HTML using data from the Caseload objects.  We can call it in the bindClickHandlers method to then complete the process that occurs when the end user clicks on the caseloads index link.

```
const bindClickHandlers = () => {
    $('.all_caseloads').on('click', (e) => {
        e.preventDefault()
        fetch(`/caseloads.json`)
            .then((res) => res.json())
            .then(caseloads => {
                $('#app-container').html('<ol class="collection" id="caseloads_index"></ol>')
                caseloads.forEach(caseload => {
                    let newCaseload = new Caseload(caseload)
                    let caseloadHtml = newCaseload.formatIndex()
                    $('#caseloads_index').append(caseloadHtml)
                })
            })
    })
}
```

I learned a lot about using JavaScript in a practical way to speed things up for the end user and make better use of resources that have already been generated to avoid making my application do twice the amount of work necessary to serve the requested information.

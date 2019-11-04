---
layout: post
title:      "MRW Using React"
date:       2019-11-04 02:08:32 +0000
permalink:  mrw_using_react
---

Working with React has been like...

<div style="text-align: center">
<img src="/img/Image.jpeg" alt="Amass the Components" />
</div>
<br />

Seriously though, understanding React, and by extension Redux, has been challenging, but feels like the culmination of my journey to learn full stack development, which is fitting, as it is the last section in my curriculum with Flatiron School.  The final project was the first time when the back end and front end felt distinct, and they were by virtue of setting up two separate applications.  Even with the previous project in which I was tasked with refactoring my Rails application to have a JavaScript component that allowed a user to interact with my app without page reloads, all of the code was contained within the same project, blurring the lines between what the Rails component was doing and what the JavaScript components were doing.  In my current project, the back end is comprised of a Rails API that handles all of the data manipulation based on input entered on the front end, which is comprised of React components and a Redux store.  Because of the setup of the applications, the principle of separation of concerns that has been emphasized throughout the curriculum feels more relevant than ever.

For my project, I chose to create a bookmark application in the same vein as Pocket wherein a user can create bookmarks and tags, or in my case folders, to organize them.  The Rails back end API processes requests from the front end and, as with any other Rails app, the controller dictates the response and persists any changes to the database.

```
# /bookmark-backend/app/controllers/api/v1/bookmarks_controller.rb

class Api::V1::BookmarksController < ApplicationController

    before_action :set_folder

    def index
        @bookmarks = @folder.bookmarks
        render json: @bookmarks
    end

    def all_bookmarks
        @bookmarks = Bookmark.all.order(:name)
        render json @bookmarks
    end

    def show
        @bookmark = @folder.bookmarks.find(id: params[:id])
        render json: @bookmark
    end

    def create
        @bookmark = @folder.bookmarks.new(bookmark_params)
        if @bookmark.save
            render json: @folder
        else
            render json: {error: "Error creating bookmark"}
        end
    end

    def destroy
        bookmark = Bookmark.find(params["id"])
        folder = Folder.find(bookmark.folder_id)
        bookmark.destroy
        render json: folder
    end

    private

    def set_folder
        @folder = Folder.find(params[:folder_id])
    end

    def bookmark_params
        params.require(:bookmark).permit(:folder_id, :name, :url, :notes)
    end

end
```

The app functions such that a user must create at least one folder before creating and adding bookmarks, but once a folder exists, the controller sets the selected folder to an instance variable that the controller can use to access the bookmarks within in each method as necessary.  Because the Rails application is not responsible for rendering views as it otherwise might be without another front end interface in place, each method renders a JSON response that the front end uses to populate the view with the requested data.

On the front end, React renders components for the user to engage in common actions such as viewing all of the bookmarks within a folder or, for example, creating a new bookmark.

```
# /bookmark-frontend/src/components/bookmarkInput.js

import React from 'react';
import {connect} from 'react-redux'
import {addBookmark} from '../actions/addBookmark'
import Form from "react-bootstrap/Form";
import Button from "react-bootstrap/Button";
import ListGroup from "react-bootstrap/ListGroup";

class BookmarkInput extends React.Component {

    state = {name: '', url: '', notes: ''}

    handleOnChange = (event) => {
        this.setState({[event.target.name]: event.target.value})
    }

    handleOnSubmit = (event) => {
        event.preventDefault()
        this.props.addBookmark(this.state, this.props.folder.id)
        this.setState({name: '', url: '', notes: ''})
    }

    render() {
        return (
            <div className="container">
                <Form onSubmit={this.handleOnSubmit}>
                    <Form.Group>
                        <ListGroup>
                        <ListGroup.Item><strong>Add a New Bookmark</strong></ListGroup.Item><br /></ListGroup>
                        <Form.Control type="text" name="name" onChange={this.handleOnChange} placeholder="Name" value={this.state.name}/><br/>
                        <Form.Control type="text" name="url" onChange={this.handleOnChange} placeholder="URL" value={this.state.url}/><br/>
                        <Form.Control as="textarea" rows="3" name="notes" onChange={this.handleOnChange} placeholder="Notes" value={this.state.notes}/><br/>
                        <Button variant="primary" type="submit">
                            Add Bookmark
                        </Button>
                    </Form.Group>
                </Form>
            </div>
        )
    }
}

export default connect(null, {addBookmark})(BookmarkInput)
```

Essentially the bookmark input initiates with a local state containing keys with empty string values that will be updated every time the user enters new or changes existing input within a field.  Once the user submits the form, the handleOnSubmit function prevents the default submission response that would redirect the user to a new page and instead uses the imported addBookmark action along with the data saved in local state to send a POST request to the Rails API.

```
# /bookmark-frontend/src/actions/addBookmark.js

export function addBookmark(bookmark, folderId) {
    return (dispatch) => {
        fetch(`http://0.0.0.0:3000/api/v1/folders/${folderId}/bookmarks`, {
            headers: {
                'Content-Type': 'application/json',
                'Accept': 'application/json'
            },
            method: 'POST',
            body: JSON.stringify(bookmark)
        })
        .then(res => res.json())
        .then(bookmark => dispatch({type: 'ADD_BOOKMARK', payload: bookmark}))
    }
}
```

The Redux Thunk middleware allows us to then use the action to return a dispatch function that awaits a response from the API before actually dispatching the payload to our reducer.  The manner in which Redux functions allows the app to make this asynchronous request, meaning that the response as we noted is not immediate and needs to be waited on before moving forward.

Once sent, the action type and payload from the dispatch trigger the reducer to update the state and refresh the components rendered on our page, which now shows the newly added bookmark!

```
# /bookmark-frontend/src/reducers/folderReducer.js

export default function folderReducer(state = {folders: []}, action) {
    switch (action.type) {

        ...

        case 'ADD_BOOKMARK':
            let folders = state.folders.map(folder => {
                if (folder.id === action.payload.id) {
                    return action.payload
                }
                else {
                    return folder
                }
            })
            return {...state, folders: folders}
        
        ...

        default:
            return state
    }
}
```
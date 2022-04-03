## AI Search Algorithm Visualisation

Hello! This is the documentation detailing the codebase of this project. For learning how to use the app, checkout the tutorial section that displays when you enter it.

## Brief Overview Of Project

This project implements interactive visualisers for algorithms that solve three broad classes of AI problems – graph search, constraint satisfaction and adversarial search. This tool is designed to help students of AI understand the algorithms better, aiding the learning process. Graph search is visualised through applying it to a 2D grid. Constraint satisfaction is visualised through applying it to the N-Queens problem, which involves placing N queens on a N x N chessboard such that no two queens are attacking each other. Adversarial search visualises the minimax algorithm through an abstract tree diagram similar to what you'd see in a standard AI textbook.

## Building Project

If you just want to view the project, you use the links [pathfinding](https://marklee7916.github.io/AI-Search-Algorithm-Visualisation/public/#/pathfinding), [constraint satisfaction](https://marklee7916.github.io/AI-Search-Algorithm-Visualisation/public/#/n-queens) and [adversarial search](https://marklee7916.github.io/AI-Search-Algorithm-Visualisation/public/#/minimax). The source code can be found [here](https://github.com/MarkLee7916/AI-Search-Algorithm-Visualisation/tree/master). To build this project from scratch, you should have node and the Angular CLI installed. The versions I use can be seen below.

`Angular CLI: 11.2.11`

`Node: 16.14.0`

Once you have these installed, you should run `npm install` to install the dependencies and then `ng serve` to serve up the project on localhost. You can build a production version using `npm run-script build`. The output can be found in the public folder. You can run the tests by running `ng test`.

## General architecture - Modules

This project uses the Angular module system. Each class of algorithm visualised has its own module and its own page, and there is a module for components that are shared between the other modules.

![](./images/module-structure.png)

Each algorithm module has its own route, organised using Angular router. Routes are handled by having a router outlet in the app folder and a routing.module file for each algorithm module. To add a new route, a maintainer would simply have to generate a routing module by running:

`ng generate module "module-name" --routing`

and then generate an entry component by running

`ng component "module-name"/"component-name"`

which would place the component in the newly created modules folder. They would then have to set up the routes in the routing.module file of the newly created module, routing their URL of choice to the new component they have created. An example of how to do this can be found in any of the routing.module files for the algorithm modules.

If you want to use a component from the shared module, remember to import it in your modules' routing.module file.

## General architecture - Dropdowns

To make adding new options easy, options for the UI are driven by dropdowns. This also helps keeps the UI consistent, making it easy for the user to find what they need by scanning through the menu. There is a dropdown component in the shared module that takes several arguments. The first argument is a list of strings that correspond to items in the dropdown menu, where each string is the option the user can see. The second argument is the current item being displayed, which should be tied to a piece of state in the parent component. This should also be a member of the list of strings. For each tool, this is conventionally done by creating an enum for each dropdown menu. These enums are typically contained in their own file and map onto the strings that will render for the options on the screen.

![](./images/dropdown-item-enums.png)

There is a piece of state in the parent component for each enum.

![](./images/dropdown-enum-states.png)

The current value of that state is passed in to the dropdown component so it knows what is currently selected. The list of strings is passed in by saving an invariant instance variable that is assigned to Object.value(Enum), which enumerates the possible string displays.

![](./images/dropdown-item-values.png)

The dropdown menu also takes a callback for notifying the parent component that the item has been updated and some commentary that displays when an item is hovered over. The image below shows how to put this all together to render a dropdown in the component.html file.

![](./images/dropdown-render.png)

## General architecture - Animations

Animations are done through rendering an animation component in the shared module and passing the appropiate arguments in. This allows someone creating a new visualiser to get animations working with very little code, although it's not perfect and a bit of boilerplate is needed. The animations are designed to support lazy computation by default. This is where animations aren't computed until the user changes the animation frame they're on. The purpose of this is to stop the browser lagging when the user makes rapid changes to the problem state.

![](./images/render-animation-component.png)

The arguments isAnimationRunning, animationIndex and animationFrames should be defined as state in the parent component. The variable isAnimationRunning will be useful in the parent component for doing conditional rendering depending on whether the animation is running. The methods setAnimationRunning() and setAnimationIndex() should be simple setters for allowing the animation component to update the values of isAnimationRunning and animationIndex. The reason updateAnimationFramesIfNeeded() isn't done through an event emitter like the other two is because it doesn't pass any parameters back up; it just gives the parent component a notification that the frames might need to be updated. updateAnimationFramesIfNeeded() should be an implementation of updating the animation frames, as it's called whenever the user changes the animation frame they're on. Typically, this involves running whatever algorithm you want to visualise and gathering a list of states you want to animate. The user should have a boolean flag in the parent that's set to true whenever the state of the problem changes (for example, the user has changed from BFS to DFS) and only computes a new animation if this flag is true. The argument needToUpdateAnimationFrames should be this boolean flag. If not using lazy loading, the user can just call updateAnimationFramesIfNeeded() after it sets the needToUpdateAnimationFrames variable to `true` so the animations are recomputed immediately. The updateAnimationFramesIfNeeded() method should also set needToUpdateAnimationFrames to `false` after it runs so that animations aren't being recomputed unnecessarily. An example of this can be seen below.

![](./images/update-animation-frames.png)

## General architecture - Generic Utilities

The shared module also has a file called "genericUtils.ts". This file contains lots of simple functions that may be useful, such as filtering a string or generating a list of numbers between two given values. It's worth checking if this file has the function you need before implementing it yourself.

## General architecture - Modals

By convention, modals are implemented by creating a component that extends AbstractModalComponent. AbstractModalComponent supplies functionality for closing modals and flipping though different modal slides, as well as a good amount of styling. If they want to render multiple slides, the parent component should pass in a list of strings identifying slides they'd like to render and then conditionally render the slides based on the values of slides\[slidesIndex\] of which an example can be seen below. This isn't necessary if you only want to render one static modal. You can switch between slides by setting up buttons that call nextSlide() and prevSlide(). You can also set up a button that calls hideModal() to close the modal. Similar to dropdowns, this can also be done by using an enum mapped onto strings that act as unique identifiers as seen in the example below.

![](./images/render-animation-component.png)

The parent component should also pass in a callback for hiding the modal. This is typically done by setting up a flag that conditionally renders the modal component and making the callback set this value to `false`.

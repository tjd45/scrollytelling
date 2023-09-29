# HW 4: Implement a Scrollytelling Experience
In this homework you will implement a scrolly-telling experience where a user can click through a poem and have their attention drawn to specific parts whilst having related visualisations display and animate throughout the process.

## Starter Code
You will be given a significant amount of starter code in this tutorial especially to deal with the formatting and display of the web page. When you load the tutorial starter code initially it should display a web page that looks like this:

*** INSERT SCREENSHOT ***

I encourage you to read over the starter code in depth and familiarise yourself with how it works. In particular spend some time playing around with the CSS file and read the comments in this file to understand how this achieves the layout that you begin with.

The header of this page holds the title of the exprerience: *Roses: A Scrollytelling Experience*. The left hand, orange, panel holds the poem that we are displaying, and the right hadn dark green panel will eventually hold the visualisations that we want to associate with the poem. The footer of the page contains two buttons a forward button and a backward button. In a more complete scrollytelling experience you would want to bind the behaviour we will code to these buttons to the scrollwheel of the user, but in our example we will just work with these buttons.

## Scrollytelling - Updating the Text ##
As you can see in the starter code we have a poem that is being displayed utilising the approach we demonstrated in the first homework using \<ul elements to represent the verses and \li elements within each \<ul> to hold the lines of the verses:

```html
<ul class="verse" id="verse1">
            <li class="line" id="line1">Roses are red</li>
            <li class="line" id="line2">And other colours too</li>
            <li class="line" id="line3">But out in nature</li>
            <li class="line" id="line4">You won't find them blue</li>
</ul>

<ul class="verse" id="verse2">
            <li class="line" id="line1">Violets <em>are</em> blue</li>
            <li class="line" id="line2">But typically purple</li>
            <li class="line" id="line3">Unfortunately for this poem</li>
            <li class="line" id="line4">Nothing rhymes with purple</li>
</ul>

<ul class="verse" id="verse3">
            <li class="line" id="line1">Now back to roses</li>
            <li class="line" id="line2">The classics are red</li>
            <li class="line" id="line3">Though white are quite trendy</li>
            <li class="line" id="line4">You can see there's quite a spread</li>
</ul>

<ul class="verse" id="verse4">
            <li class="line" id="line1">Roses are red</li>
            <li class="line" id="line2">Never really blue</li>
            <li class="line" id="line3">Let's put these bars in order</li>
            <li class="line" id="line4">So the graph looks nice too</li>
</ul>
```

One of the first steps in designing a scrollytelling experience is to decide exactly how you want to display the experience to your user. In our case we want to decide which aspects of the poem you want to highlight to draw the users attention. This can be done on multiple levels of granularity, from whole verses, to selections of lines all the way down to individual words. We will define each step of this process as a *keyframe*. In each keyframe we will want to either update the text that is the focus, or update the associated visualisation, or (oftentimes) do both at the same time.

In the first part of this tutorial we are just going to focus on updating the highlighted text. In our example I would like to first highlight the whole of verse one, then the whole of verse two. After this I would like to step through each line of verses 3 and 4 in turn.

To achieve this we are going to define a ```keyframes``` data structure and a ```drawKeyframes``` function.

```Javascript
let keyframes = [
    {
        activeVerse: 1,
        activeLines: [1, 2, 3, 4]
    },
    {
        activeVerse: 2,
        activeLines: [1, 2, 3, 4]
    },
    {
        activeVerse: 3,
        activeLines: [1]
    },
    {
        activeVerse: 3,
        activeLines: [2]
    },
    {
        activeVerse: 3,
        activeLines: [3]
    },
    {
        activeVerse: 3,
        activeLines: [4]
    },
    {
        activeVerse: 4,
        activeLines: [1]
    },
    {
        activeVerse: 4,
        activeLines: [2]
    },
    {
        activeVerse: 4,
        activeLines: [3]
    },
    {
        activeVerse: 4,
        activeLines: [4]
    }
]
```

For each keyframe we define the active verse and the active lines that we would like to highlight to the user.

For the ```drawKeyframe``` function we will need to call other functions that we will initally write as dummy functions which do nothing and then go and fill them in one by one.

```Javascript
// We need to define a keyframe index globally to keeep track of where we are at in the narrative flow
let keyframeIndex = 0

function drawKeyframe(kfi){
  // Get the current keyframe 
  let kf = keyframes[kfi];

  // Reset any lines that are currently active
  resetActiveLines();

  // Update which verse is currently being displayed as active
  updateActiveVerse(kf.activeVerse);

  // Iterate over the active lines for this keyframe and update the active lines one by one
  for (line of kf.activeLines) {
        updateActiveLine(kf.activeVerse, line);
  }
}

function resetActiveLines(){
  return
}

function updateActiveVerse(id){
  return
}

function updateActiveLine(vid,lid){
  return
}
```

In order to successfully update and highlight the appropriate verses and lines we are going to want to make use of the css classes and ids we have assigned to each of the verses and lines and apply new css classes to the relevant verses/lines to indicate they are now active. We could do this using pure javascript or utilise d3's built in functionality. Today we are going to use d3.

```Javascript
function resetActiveLines() {
  // Reset the active-line class for all of the lines
  d3.selectAll(".line").classed("active-line", false);
}

function updateActiveVerse(id) {
  // Reset the current active verse - in some scenarios you may want to have more than one active verse, but I will leave that as an exercise for you to figure out
  d3.selectAll(".verse").classed("active-verse", false);

  // Update the class list of the desired verse so that it now includes the class "active-verse"
  d3.select("#verse" + id).classed("active-verse", true);

}

function updateActiveLine(vid, lid) {
  // Select the correct verse
  let thisVerse = d3.select("#verse" + vid);
  // Update the class list of the relevant lines
  thisVerse.select("#line" + lid).classed("active-line", true);
}
```

This is basically all of the code that we need to kick off a very basic scrollytelling experience. All we need to do now is implement a function to detect whether the user has clicked forward or backward on the page:

```Javascript
function forwardClicked() {

  // Make sure we don't let the keyframeIndex go out of range
  if (keyframeIndex < keyframes.length - 1) {
    keyframeIndex++;
    drawKeyframe(keyframeIndex);
  }
}

function backwardClicked() {
  if (keyframeIndex > 0) {
    keyframeIndex--;
    drawKeyframe(keyframeIndex);
  }
}
```

Remember to tie these functions to the html buttons in the body of your javascript file:

```Javascript
document.getElementById("forward-button").addEventListener("click", forwardClicked);
document.getElementById("backward-button").addEventListener("click", backwardClicked);
```

When you go and click these buttons now nothing will change visually but you should see in the inspector that the classes of the relevant verses and lines are being updated. Now we we need to go and define some css classes so that this change in class is reflected visually:

```css
.active-verse {
  font-weight: 900;
  color: #555;
}

.active-line {
  color: black;
}
```

These two classes will change the colour of the font and increase the weight of the active verse when they are updated. The last step to finishing the preliminary scrollytelling experience is to draw the first keyframe when the page loads. To do this we are going to define an ```initialise``` function to handle anything that we would like to do right when the page loads. At the moment all this entails is drawing the first keyframe which we can do using the global keyframe index that we have defined:

```Javascript
function initialise() {
    drawKeyframe(keyframeIndex);
}

initialise();
```

Now when we load the webpage we should see that the first verse is in a heavier font and all highlighted in black. Clicking the forward and backward buttons should update this in line with our keyframes.

This is a good start but it is a little bit clumsy. Let's start by tidying up the visualsa  little bit so that the experience is a little bit less messy. Firstly, because I want this experience to be a seamless full-screen experience for a user I don't want their to be a scrollbar that appears on the side of the page. To achieve this we are going to force the left column content div to have a maximum height like this:

```css
.left-column-content{
    // This forces the maximum height of the div to be equal to 100 % of the view height (the current size of the screen)
    // the -150px is the size of the header and the footer combinedd
    // You should be very careful when writing css like this as there are potential complications you can run into
    // when using viewport units (vh) and you'll also need to be careful that if you update the height of the
    // header or footer that you are sure to update the value here.
    max-height: calc(100vh - 150px);
}
```

As you should see this kind of works but now our page looks even messier as even though the size of the div has been fixed, the ccontent of the div is overflowing out of it and making our page look messy. To fix this let's set the ```overflow``` css property to handle this.

```css
.left-column-content {
  max-height: calc(100vh - 150px);
  overflow: auto;
}
```

This is much better, but having the scrollbar appear on the div is quite messy. Let's add some more css to hide the scrollbar whilst still allowing the user to scroll. To do this we need to access the ```-webkit-scrollbar``` css class. This may differ from browser to browser, but this is how you can achieve this when using Chrome:

```css
.left-column-content::-webkit-scrollbar {
  width: 0; /* Hide the scrollbar's width */
  height: 0; /* Hide the scrollbar's height */
}
```

For more information on designing custom scrollbars and which browsers support what, you can look at this tutorial: https://www.w3schools.com/howto/howto_css_custom_scrollbar.asp. 

*** TIDY UP LINK ***

So now our page looks a lot nicer, but ideally we would like the page to automatically scroll so that when the user moves to the next keyframe the relevant text is in the middle of the screen. To do this, let's define a function called ```scrollLeftColumnToActiveVerse``` which will do exactly what it sounds like. We we will call this function every time that the active verse is updated. The code should look something like this. Take a moment to read the code carefully and sketch it out if you want to get your head around exactly why the calculations work.

```Javascript
function scrollLeftColumnToActiveVerse(id) {
    // First we want to select the div that is displaying our text content
    var leftColumn = document.querySelector(".left-column-content");

    // Now we select the actual verse we would like to be centred, this will be the <ul> element containing the verse
    var activeVerse = document.getElementById("verse" + id);

    // The getBoundingClientRect() is a built in function that will return an object indicating the exact position
    // Of the relevant element relative to the current viewport.
    // To see a full breakdown of this read the documentation here: https://developer.mozilla.org/en-US/docs/Web/API/Element/getBoundingClientRect
    var verseRect = activeVerse.getBoundingClientRect();
    var leftColumnRect = leftColumn.getBoundingClientRect();

    // Now we calculate the exact location we would like to scroll to in order to centre the relevant verse
    // Take a moment to rationalise that this calculation does what you expect it to
    var desiredScrollTop = verseRect.top + leftColumn.scrollTop - leftColumnRect.top - (leftColumnRect.height - verseRect.height) / 2;

    // Finally we scroll to the right location using another built in function.
    // The 'smooth' value means that this is animated rather than happening instantly
    leftColumn.scrollTo({
        top: desiredScrollTop,
        behavior: 'smooth'
    })
}

function updateActiveVerse(id) {
    // Reset the current active verse - in some scenarios you may want to have more than one active verse, but I will leave that as an exercise for you to figure out
    d3.selectAll(".verse").classed("active-verse", false);

    // Update the class list of the desired verse so that it now includes the class "active-verse"
    d3.select("#verse" + id).classed("active-verse", true);

    // Scroll the column so the chosen verse is centred
    scrollLeftColumnToActiveVerse(id);
}
```
This is almost complete but it would look a little bit nicer if the first and last verses were also centred rather than being at the top and bottom of the div. To achieve this let's add a couple of dummy verses to our html:

```html
<div class="filler-verse"></div>
<ul class="verse" id="verse1">
            <li class="line" id="line1">Roses are red</li>
            <li class="line" id="line2">And other colours too</li>
            <li class="line" id="line3">But out in nature</li>
            <li class="line" id="line4">You won't find them blue</li>
</ul>

<ul class="verse" id="verse2">
            <li class="line" id="line1">Violets <em>are</em> blue</li>
            <li class="line" id="line2">But typically purple</li>
            <li class="line" id="line3">Unfortunately for this poem</li>
            <li class="line" id="line4">Nothing rhymes with purple</li>
</ul>

<ul class="verse" id="verse3">
            <li class="line" id="line1">Now back to roses</li>
            <li class="line" id="line2">The classics are red</li>
            <li class="line" id="line3">Though white are quite trendy</li>
            <li class="line" id="line4">You can see there's quite a spread</li>
</ul>

<ul class="verse" id="verse4">
            <li class="line" id="line1">Roses are red</li>
            <li class="line" id="line2">Never really blue</li>
            <li class="line" id="line3">Let's put these bars in order</li>
            <li class="line" id="line4">So the graph looks nice too</li>
</ul>
<div classs="filler-verse"></div>
```
Now let's add some css to make this filler verse take up some space:
```css
.filler-verse {
  height: 500px;
}
```
That should be it for the text display aspect of the scrollytelling experience. As we are doing all the scrolling for the user we could actually set the overflow for the ```left-column-content``` to be equal to ```hidden``` and this would mean a user can't see the overflow and can't scroll manually. It would also remove the scrollbar altogether.

The final little change we are going to make is to ensure that the content always fills the screen. We'll do this in case we are displaying a small poem, or we are using a big screen. Again, a reminder that you should use viewport units and this kind of css sparingly.

```css
.wrapper {
  display: flex; /* Allows us to position things inside this div with more freedom, see https://css-tricks.com/snippets/css/a-guide-to-flexbox/ for more details*/
  flex-grow: 1; /* The div will now grow to fill the available space */

  /* vh is the viewheight, you should use this VERY sparingly, this situation is ok because we're limiting what the user can do */
  /* we'll minus 150px as this is the height we have manually defined the header and footer to be combined (100px + 50px)*/
  /* If we changed the height of the header and footer we'd have to change this here too! */
  min-height: calc(100vh - 150px);
}
```
## Scrollytelling - Updating Visualisation to go with the Text
Now that we have finished how the text should display for our scrollytelling, the next step is to update the right hand panel of the screen to display and change visualisation associated with each keyframe.


## Where to go from Here:

For full credit on this assignment, you will need to complete the tutorial. You will not need to do anything extra to get full-credit -- the tradeoff being that this assignment introduces a lot more complexity:

1. [50%] **Visible Network**. The node-link diagram is drawn using the force-directed approach specified in the tutorial (though you can feel free to play around with the parameters or add additional forces).
1. [25%] **Interactive Network**. The user can drag nodes. The user can pan and drag the entire layout.
1. [15%] **Visible Tree**. The tidy tree layout is created as specified in the tutorial.
1. [10%] **Interactive Tree**. Hovering over any node in the tree highlights the same node in the node-link diagram and vice-versa.
1. Extra Credit [+5%]: **Customized Look** Draw the nodes in the node-link diagram differently depending on the attributes (e.g., gender, class) or network metrics (e.g., out-degree). If color is used, it should be consistent between the network and tree. You can also consider styling the edges of the node-link diagram instead: edge weight and direction are both good options for this.
1. Extra Credit [+5%]: **Tree from Network** When the user clicks on a node in the node-link diagram, the tree for that node is drawn. If a tree was already there, it is removed (e.g., with `remove()` or a potentially more elegant update-based transitions) and replaced with the new one.

For example, in the image below, the colors have been assigned using the class each student belongs to. The nodes are sized by degree and the links are sized by edge weight. On hover, only the hovered node and its out-degree adjacent nodes retain their opacity, all other nodes and edges are made somewhat transparent. A stroke has been added to the tree node circles. On click, the tree is drawn again for the selected node.

![extra credit](assets/images/extra_credit.png)

### Acknowledgements:
This assignment was adapted from an assignment designed by [Dr. Alex Godwin](https://www.jagodwin.com).

- Force arrangement adapted from https://observablehq.com/@borowski-9ld/d3-force-directed-graph
- Colors from http://colorizer.org/ and https://colorbrewer2.org
- Some material adapted from Interactive Data Visualization for the Web, 2nd Edition, by Scott Murray
- Tree layout adapted from https://observablehq.com/@d3/tidy-tree
- Dataset from http://www.sociopatterns.org/datasets/high-school-contact-and-friendship-networks/

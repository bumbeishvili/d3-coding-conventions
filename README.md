

# d3-coding-patterns
This is short and **veeeery subjective** convention of coding with d3.js. 


1. [Boilerplate Explanations](#boilerplate-explanations)
   - [main function name](#main-function-name)
   - [attrs](#attrs)
   - [calc](#calc)
   - [behaviors](#behaviors)
   - [scales](#scales)
   - [layouts](#layouts)
   - [functions](#functions)
   - invokation
   - debugging
   - [Responsivity](#responsivity)
1. [Indentations & Formatting ](#indentations-and-formatting)
1. [Commenting](#commenting)
1. Patterns
1. [Commit Messages](#commit-messages)


# Boilerplate Explanations
I have tried to merge all best conventions and standarts in one boilerplate ... 
* [Towards reusable charts](https://bost.ocks.org/mike/chart/)
* [Towards updatable code](https://www.toptal.com/d3-js/towards-reusable-d3-js-charts)
* and some things from personal experience 


## Main Function Name 
In the boilerplate main function name is `renderChart`, but if we want to display multiple charts in the same page, based on our boilerplate, we should rename it accordingly

If we are developing pie chart , name function as `renderPieChart`.

If we are developing bar chart , name function as `renderBarChart`

## attrs
 We group hardcoded items (colors for example, also margins ...) using `attrs` object. If we are using any hardcoded item in the code, that is mistake. 
 
 
  Good Points:
+ Prevents dublication
+ Makes easy to apply changes from one place
+ These properties are transformed to dynamic functions and we can override attributes from outside


Simple `attrs` object looks like this
```javascript
  // exposed variables
  var attrs = {
    svgWidth: 400,
    svgHeight: 400,
    marginTop: 5,
    marginBottom: 5,
    marginRight: 5,
    marginLeft: 5,
    container: 'body',
    data: null
  };
```

But, as we go forward, it gets more properties:
```javascript
 var attrs = {
    id: 'id' + Math.floor((Math.random() * 1000000)),
    svgWidth: 1000,
    svgHeight: 800,
    minWindowWidth : 800,
    marginTop: 50,
    marginBottom: 50,
    marginRight: 50,
    marginLeft: 50,
    container: 'body',
    firstTime: true,
    data: null,
    spacing: {
      bar: 2,
      unit: 30,
      category: 3
    },
    slicesOpacity: 0.3,
    colors: {
      point: "grey",
      fullname: "#4B4948",
      offline: "grey",
      online: "lightgreen",
      categorytext: "",
      unitslegend: "#4B4948",
    },
    tooltipRows: [{ left: "User", right: "{fullname}" },
    { left: "Blitz", right: "{blitzrating}" },
    { left: "Bullet", right: "{bulletrating}" },
    { left: "Classical", right: "{classicalrating}" },
    { left: "Unit", right: "{unit}" }]
  };
  ```
  
 As we mentioned `attrs` objects are transformed to dinamic functions using this code
```javascript
 //dinamic functions
  Object.keys(attrs).forEach(key => {
    // Attach variables to main function
    return main[key] = function (_) {
      var string = `attrs['${key}'] = _`;
      if (!arguments.length) { return eval(` attrs['${key}'];`); }
      eval(string);
      return main;
    };
  });
```

So we can use chart reference directly to set  or get attribute properties
```javascript
//get chart reference
chart = renderChart();
   
// attribute setting
chart.svgHeight(window.innerHeight - 30)
  .svgWidth(window.innerWidth - 30)
  .container('#myGraph')
  .data(data)
  
//to get value of attrs property, don't pass param to it
var marginLeft = chart.marginLeft();
var container = chart.container();
```

## calc
For calculated properties we have `calc` object. 

**example**
```javascript
  //calculated properties
  var calc = {}
  calc.chartLeftMargin = attrs.marginLeft;
  calc.chartTopMargin = attrs.marginTop;
  calc.chartWidth = attrs.svgWidth - attrs.marginRight - calc.chartLeftMargin;
  calc.chartHeight = attrs.svgHeight - attrs.marginBottom - calc.chartTopMargin;
```

## behaviors
Behaviors comes from **d3.v3** ,although it was removed in **d3.v4**,but we still use it for grouping purposes in commenting section.

So, there are only two behaviors: [zoom](https://github.com/d3/d3-zoom) and [drag](https://github.com/d3/d3-drag), if we have to use one og them , we make them appear  under **behaviors** group comment section
```javascript
/*##################################   BEHAVIORS ####################################### */
var behaviors = {};
behaviors.zoom = d3.zoom().on("zoom", d => handlers.zoomed(d));
```

## scales
Scales are one of the main parts of d3.js, so it deserves to have own group

```javascript
/*##################################   SCALES  ####################################### */
 var scales = {}
 scales.x = d3.scaleLinear()
   .domain([0, attrs.data.values[0].lines.length - 1]) // input
   .range([0, calc.chartWidth]);
 scales.y = d3.scaleLinear()
   .domain([0, maxLineValue(attrs.data) + 2])
   .range([calc.chartHeight, 0]);
```


## layouts
Layouts, as behaviors, comes from d3.v3 and removed from d3.v4. We use layouts in group comment section

there are following layouts:
+ d3.chord()
+ d3.pie()
+ d3.hierarchy()
+ d3.histogram()
+ d3.pack()
+ d3.partition()
+ d3.stack()
+ d3.tree()
+ d3.treeMap()

If we have to use one of them, then we have to group them with group comment as well
```javascript
//#########################  LAYOUTS  ########################
var layouts = {};
layouts.pie = d3.pie().value(function (d) { return d[attrs.currentProp] }).sort(null);
```

## functions
At the end of main functions body we write helper functions (for example, long label wrapping, tooltip drawing func and e.t.c. )



It's possible to group functions with sub-group comments as well

##  Responsivity
 In order charts to be responsive, we are doing following :

1. We are setting an `id` property in `attrs` object
 ```javascript
    var attrs = {
      id: 'id' + Math.floor((Math.random() * 1000000))
    }
 ```
2. Changing width based on containers width and redrawing chart, if necessary, updating other `attrs` properties too
```javascript
 function setDimensions() {
        var width = container.node().getBoundingClientRect().width;
        main.svgWidth(width);
        
        // if width is too small, change attrs.fontSize too e.t.c
        container.call(main);
 }
```

3. Adding named listener to te `resize` event and redrawing chart
```jaavascript
  d3.select(window).on('resize.' + attrs.id, function () {
     setDimensions();
  })
```

# Indentations And Formatting
  We will use default visual studio code IDE formatter:  
  
  For mac , it's `cmd+shift+f`   
  
  For windows  `alt+shift+f`  
  
  
  apart from that, we will use two spaces after each nested statement  
  

for example  
**Instead of this**
```javascript
var quarterFormatTexts = quarterTextGroup.selectAll(".quarter-format-text")
    .data(attrs.data.bars)
    .enter()
    .append("text")
    .attr('class', 'quarter-format-text')
    .attr('text-anchor', 'middle')
    .attr('fill', attrs.formatTextColor)
    .attr('font-size', attrs.formatTextFontSize)
    .attr('y', 15);
```
**we will use that**
```javascript
var quarterFormatTexts = quarterTextGroup.selectAll(".quarter-format-text")
  .data(attrs.data.bars)
  .enter()
  .append("text")
  .attr('class', 'quarter-format-text')
  .attr('text-anchor', 'middle')
  .attr('fill', attrs.formatTextColor)
  .attr('font-size', attrs.formatTextFontSize)
  .attr('y', 15);
```


# commenting 

We have 3 kind of comments
1. Block Comments
 ```javascript
 //#########################    BLOCK COMMENT WITH UPPERCASE   ######################
 ``` 
2. Sub Block Comments
 ```javascript
 //-----------------    This Is Sub Block Comment With Camelcase   ------------------
 ``` 
 
3. Statement level comment
 ```javascript
 //this is statement level comment
 ```
 
 We are dividing code int blocks, sub blocks and statements.  
 **Before comment should be left one blank line!**
 **Each block, sub block and statement should be commented out!**
 
 I am repeating:  
 **Each block, sub block and statement should be commented out!**
 
 Once again:  
  **Each block, sub block and statement should be commented out!**
  
 One more time and we will continue :
 # Each block, sub block and statement should be commented out and before comment one blank line should be left !
 
 Ok, here is example of code, which contains all type of comments
 
 ```javascript
 //#########################  SCALES  ########################
var scales = {};
scales.barWidth = d3.scaleLinear()
  .domain([4, 8])
  .range([50, 35])

//assign calc value based on scale
calc.barWidth = scales.barWidth(attrs.data.bars.length);

//y axis scale
scales.y = d3.scaleLinear()
  .range([calc.chartHeight, 0])
  .domain([0, calc.highestBarAccumulativeSum * 1.1]);

//bar height scale
scales.bar = d3.scaleLinear()
  .range([0, calc.chartHeight])
  .domain([0, calc.highestBarAccumulativeSum * 1.1]);

//###########################  AXES  ########################
var axes = {};
axes.yAxis = d3.axisRight(scales.y)
  .ticks(5)
  .tickSize(-calc.chartWidth - attrs.marginRight - 70)

//###########################  DRAWING   ########################

//----------------------  Containers ------------------

//drawing containers
var container = d3.select(this);
container.html('');

//svg drawing
var svg = d3.select(this)
  .append('svg')
  .attr('width', attrs.svgWidth)
  .attr('height', attrs.svgHeight)
  .attr('font-family', attrs.defaultFont)
  .style('overflow', 'hidden');

//inner container drawing
var chart = svg.append('g')
  .attr('transform', 'translate(' + (calc.chartLeftMargin) + ',' + calc.chartTopMargin + ')');

// ------------ actual element drawing  -------------

// quarter text wrapper
var quarterTextGroup = chart.append('g').attr('class', 'quarter-text-group');

//quarter texts
var quarterTexts = quarterTextGroup.selectAll(".quarter-text")
  .data(attrs.data.bars)
  .enter()
  .append("text")
  .attr('class', 'quarter-text')
  .text(function (d, i) { return d.label; })
  .attr('text-anchor', 'middle')
  .attr("transform", function (d, i) { return "translate(" + i * (calc.distanceBetweenBars) + "," + (calc.chartHeight + 20) + ")"; })
  .attr('fill', attrs.defaultTextColor)
  .attr('font-size', attrs.defaultTextFontSize);

//format texts
var quarterFormatTexts = quarterTextGroup.selectAll(".quarter-format-text")
  .data(attrs.data.bars)
  .enter()
  .append("text")
  .attr('class', 'quarter-format-text')
  .text(function (d, i) { return d.format; })
  .attr('text-anchor', 'middle')
  .attr("transform", function (d, i) { return "translate(" + i * (calc.distanceBetweenBars) + "," + (calc.chartHeight + 20) + ")"; })
  .attr('fill', attrs.formatTextColor)
  .attr('font-size', attrs.formatTextFontSize)
  .attr('y', 15);

//right divider grid
var rightGrid = chart.append('rect')
  .attr('width', attrs.gridWidth)
  .attr('height', calc.chartHeight)
  .attr('x', calc.chartWidth - calc.leftLegendWidth)
  .attr('fill', attrs.gridColor)

//------------------   axes --------------------

//axis wrapper
var axisGroup = chart.append("g");

//axis elements
var axis = axisGroup.call(axes.yAxis);

//axis styles
axis.attr("transform", "translate(" + calc.chartWidth + "," + 0 + ")")
axis.selectAll(".domain").attr("opacity", 0);
axis.attr('fill', attrs.axisTextColor)
svg.selectAll('.tick text')
  .attr('fill', attrs.axisTextColor)
  .attr('font-size', attrs.defaultTextFontSize)
  .attr('text-anchor', 'end')
  .attr('x', 30)
  .attr('y', -10)

//tick line restyling
svg.selectAll('.tick line').attr('transform', 'translate(40)')
  .attr('stroke', attrs.gridColor)
  .attr('stroke-width', attrs.gridWidth)
 ```
 


# Commit messages
Based on chart we are developing, commit message has following structuren  

Chart Name - actual message

For example, if we are developing bar chart and in cimmit we are adding code which draws bars,

commit message would looks like:  
`bar chart - draw bars`


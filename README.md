# d3-coding-patterns


1. [Boilerplate Explanations](#boilerplate-explanations)
   - [main function name](#main-function-name)
   - [attrs](#attrs)
   - [calc](#calc)
   - behaviors
   - scales
   - layouts
   - functions
   - invokation
   - debugging
   - [Responsivity](#responsivity)
1. [Indentations & Formatting ](#indentations-and-formatting)
1. [Commenting](#commenting)
1. Patterns
1. [Commit Messages](#commit-messages)


# Boilerplate Explanations

## Main Function Name 
In the boilerplate main function name is `renderChart`, but if we want to display multiple charts in the same page, based on our boilerplate, we should rename it accordingly

if we are developing pie chart , name function as `renderPieChart`.

if we are developing bar chart , name function as `renderBarChart`

## attrs
 We group hardcoded items (colors for example, also margins ...) using `attrs` object. If we are using any hardcoded item in the code, that is mistake. 
  Good Points:
+ prevents dublication
+ makes easy to apply changes from one place
+ these properties are transformed to dinamic functions and we can override attributes from outside


simple `attrs` object looks like this
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

but as we go forward, it gets more properties
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
  
  as we mentioned `attrs` objects are transformed to dinamic functions using this code
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

so we can use chart reference directly to set  or get attribute properties
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
for calculated properties we have `calc` object. 

**example**
```javascript
  //calculated properties
  var calc = {}
  calc.chartLeftMargin = attrs.marginLeft;
  calc.chartTopMargin = attrs.marginTop;
  calc.chartWidth = attrs.svgWidth - attrs.marginRight - calc.chartLeftMargin;
  calc.chartHeight = attrs.svgHeight - attrs.marginBottom - calc.chartTopMargin;
```




##  Responsivity
 In order, charts to be responsive, we are doing following :

1. we are setting an `id` property in `attrs` object
 ```javascript
    var attrs = {
      id: 'id' + Math.floor((Math.random() * 1000000))
    }
 ```
2. changing width based on containers width and redrawing chart, if necessary, updating other `attrs` properties too
```javascript
 function setDimensions() {
        var width = container.node().getBoundingClientRect().width;
        main.svgWidth(width);
        
        // if width is too small, change attrs.fontSize too e.t.c
        container.call(main);
 }
```

3. adding named listener to te `resize` event and redrawing chart
```jaavascript
  d3.select(window).on('resize.' + attrs.id, function () {
     setDimensions();
  })
```

# Indentations And Formatting
  We will use default IDE formatter, for visual studio code, it's `cmd+shift+f` for mac and `alt+shift+f` for windows , apart from that, we will use two spaces after each nested statement

for example  
**instead of this**
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

we have 3 kind of comments
1. Block Comments
 ```javascript
 //#########################    BLOCK COMMENT WITH UPPERCASE   ######################
 ``` 
2. Sub Block Comments
 ```javascript
 //-----------------    This Is Sub Block Comment With Camelcase   ------------------
 ``` 
 
3. statement level comment
 ```javascript
 //this is statement level comment
 ```
 
 We are dividing code int blocks, sub blocks and statements.  
 **before comment should be left one blank line!**
 **Each block, sub block and statement should be commented out!**
 
 I am repeating:  
 **Each block, sub block and statement should be commented out!**
 
 Once again:  
  **Each block, sub block and statement should be commented out!**
  
 One more time and we will continue :
 # Each block, sub block and statement should be commented out and before comment one blank line should be left !
 
 ok, here is example of code, which contains all type of comments
 
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
Based on chart we are developing, commit message has following structure
Chart Name - actual message

for example, if we are developing bar chart and in cimmit we are adding code which draws bars,

commit message would looks like:  
`bar chart - draw bars`


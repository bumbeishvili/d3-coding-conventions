# d3-coding-patterns


1. Boilerplate Explanations
   - attrs
   - calc
   - behaviors
   - scales
   - layouts
   - functions
1. [Indentations & Formatting ](#indentations-and-formatting)
1. Commenting
1. Patterns
1. Responsivity



## Indentations And Formatting
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


## commenting 

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
 
 ```
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

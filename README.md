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

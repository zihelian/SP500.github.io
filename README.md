# CPSC436V-Project Group 6: S&P 500 Visualization

### Name: Tianhang Cui
#### Student Number:32968299
#### CSID: y7r1b
  
### Name: Yuchen Liu
#### Student Number: 19279158
#### CSID: d6h0b
  
### Name: 
#### Student Number:
#### CSID: 

# I. External Sources Used:
### (The change from the cited resources is included in the next part)
#### lineChart Animation:
https://observablehq.com/@jurestabuc/animated-line-chart
#### dragging:
https://observablehq.com/@d3/circle-dragging-i
#### brush for the line chart
https://codesandbox.io/s/github/UBC-InfoVis/2021-436V-examples/tree/master/d3-brushing-linking?file=/js/focusContextVis.js
#### treemap
https://www.d3-graph-gallery.com/treemap
#### bubbleChart
https://codesandbox.io/s/github/UBC-InfoVis/2021-436V-examples/tree/master/d3-interactive-scatter-plot
https://www.freecodecamp.org/news/get-ready-to-zoom-and-pan-like-a-pro-after-reading-this-in-depth-tutorial-5d963b0a153e/

# II. High-level Process/Changes Made:
css/style.css:
-
- style the visualization.

data/436_preprocess.ipynb:
-
- preprocess, merge and trim csv data. Detailed steps are documented in M2 writeup and inside the Jupyter Notebook.

js/preprocess.js:
-
- csv data is converted into json object for better performance and faster data lookup
- Derive stock index for each sector and store data as json object


js/main.js:
-
- define global variable such as color scale and time interval
- load data and initialize views
- handle search submission and update line chart view
- getbubbleChartData() function computes stock price percentage change for bubble chart base on the given interval
- filterSector() filter bubble chart data points based on the selected sector in tree map

js/treeMap.js
- 
- referred to treemap examples in https://www.d3-graph-gallery.com/treemap\
- render treemap and show tooltips on a hover
- implemented filter interaction on a sector selection

js/lineChart.js
- 
####For the initVis():
- I first updated both y-scale and x-axis to be scaleLinear() since the both of them are quantitative.   
- I formatted the tick marks to be long enough so that it can be used the see the position.
- I set and update all my scales, making sure to update my scales in updateVis in case any data has changed
- I added the vertical and horizontal axis for displaying the date and the stock price. I put the axes into their corresponding group.
- I added the legend to indicate the meaning of color and dash lines: vis.add_legend()
- I initialized the brush so that we can modify it later

####For the updateVis():
- There are some variable component in the title that need to be updated here based on the data.
- I mainly set the domain of the axes as they depend on the input data.
- Based the selection, vis.import_data() will select only the data needed and put them into vis.selected_stock_data
- As the title and the axis name will sometimes change based on the selection(i.e sp500 index, sector price or company price), I update them here by vis.update_Title_and_AxisName()
####  For the renderVis():
- It first draws the line in the overview line chart as it is simple and static.
- Then it updated the function of hovering mouse on the chart.
- It then will set the brush to a pre-specified place, as it set the position of the brush, the line on the detailed chart will be drawn as the 
function vis.brushed will be called and it then calls renderline to draw the line.

js/bubbleChart.js
-
- initialize the bubblechart based on https://codesandbox.io/s/github/UBC-InfoVis/2021-436V-examples/tree/master/d3-interactive-scatter-plot and Programming Assignment 2.
- user can click to select/unselect bubble for detailed trend on linechart via `clickBubble()`.
- hover over each bubble, the app will display detailed tooltips for each company. Implementation are in `showTooltip()` and `hideToolTip()`.
- d3 zoom referred to https://www.freecodecamp.org/news/get-ready-to-zoom-and-pan-like-a-pro-after-reading-this-in-depth-tutorial-5d963b0a153e/
- `initialZoom()` provides the functionality of a initial zooming + panning transition when the page is loaded up, to advise the user of the functionality of zooming.
- after the data finished loading, `vis.zoom` is bound to the svg element, for user to perform interaction.
- double click, scroll using mouse, pan using mouse will zoom and pan the chart. 
- when user click on button `Reset Stocks Selection`, or click on a block of treemap, `autoZoomed()` is used to update the transition of the axis and scale.
- resize/drag the brush, click on button `Reset Stocks Selection`, and click on a block of treemap will trigger transition of circles bound in `renderVis()`.
- `Reset Stocks Selection` is bound to a click event on `vis.resetZoom()` and `vis.resetSelectedStockSymbol()`. Click on `Reset Stocks Selection` will reset the selections and viewpoint.
- For each bubble, we added the support for drag by `.call(d3.drag().…………………`,   
  - When the drag is started: It will create a new circle with clip-path on it, so that it will looks like a check mark, we will call it the "new shape"
  - When the drag is moving: The new shape will adjust the position based on the pointer: `clone.attr("cx", event.x+vis.config.margin.left).attr("cy", event.y+vis.config.margin.top)`  
  - When the drag is end: The function dragend() will be called, and it performs different action based on the final position of the drag:
    1. If it is in the area of one of the rectangle for the 'basket', we will put the symbol of this stock into the `custom_data` or `custom_data2`, and then update the line chart based on it. Also, we will let the “new shape” can be dragged so that the user can remove it from the basket.  
    2. If it is not in the area of 'basket', we will treat it as the user want to cancel the drag and nothing will happen(the "new shape" will be removed).
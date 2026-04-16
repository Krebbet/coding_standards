# Before plot creation

When it is decided that a plot would be the best way to deliver information, there are two main things to keep in mind before creating the plot:

1.  **Succinctness:** Consider the amount of information you are planning to put into a plot, as it is the first determinant of how easy it is to follow the other guidelines.
2. **Consistency:** Consider how to keep plots consistent with existing schemes (both project-specific and SFL-related).

# Succinctness

The amount of information in each plot should be succinct and focused ("bite-sized" = easier to digest).

- Figures should contain only information that is relevant to the point you are making.
- Ideally, a plot should convey one main idea at a time.
- If a plot must contain more than one idea, it should be precise and obvious. If necessary, consider having additional plots to describe/show evidence.

# Consistency

When plot formatting is consistent, the viewer can concentrate on the message revealed by differences between the plots. Plan formatting so that plots are consistent with SFL defaults and across a project.

## Netty

Where possible, begin with Netty. Browse the Netty demo notebooks (available in your project repo's `src/netty/demo/` folder) for relevant plots.

If there are relevant plots, begin with them as the template. This helps keep consistency with existing SFL plotting schemes. The code from the Netty folder in each project repository can be edited to fit to each project's specific needs, while maintaining a consistent starting point. Any unused code at code delivery should be deleted (see [here](https://www.notion.so/c27f6c79c9ad4cc9984e365384a2dc05?pvs=21)).

## SFL Matplotlib Defaults

Even when not starting from a Netty plot template, remember to keep plots consistent with SFL plotting schemes as defined in the following preamble (also available in SFL_Template [repo](https://github.com/SFLScientific/SFL-Template/blob/master/src/netty/utils/matplotlib_defaults.py) and can also be found in your project's repo in `src/netty/utils/matplotlib_defaults.py`).

```python
import matplotlib.pyplot as plt
%config InlineBackend.figure_format = 'retina'

def sfl_defaults():
	plt.style.use('classic')
	plt.rcParams['figure.figsize'] = [8.0, 5.0]
	plt.rcParams['figure.facecolor']='w'

	# text size
	plt.rcParams['xtick.labelsize']=14
	plt.rcParams['ytick.labelsize']=14
	plt.rcParams['axes.labelsize']=15
	plt.rcParams['axes.titlesize']=16
	plt.rcParams['legend.fontsize']=12

	# grids
	plt.rcParams['grid.color'] = 'k'
	plt.rcParams['grid.linestyle'] = ':'
	plt.rcParams['grid.linewidth'] = 0.5

	# 
	print('SFL style loaded...')

sfl_defaults()
```

## Define Schemes

Throughout each project, have a defined plotting scheme and stick to it. This plotting scheme includes but is not limited to color, position, font hierarchy, and font size.

### Color Scheme

Consider using SFL's color scheme OR use tools to build custom color palettes.

![](Before%20plot%20creation/Untitled.png)

```
### SFL LOGO COLORS
# 0B2F44 : sfl-dark-blue
# 379DDD : sfl-bright-blue
# F3F4F5 : sfl-light-gray
# F2C0C9 : mike-pink

### Adjusted Color Palette
# 4B8EB8 : adjusted-palette-blue
# 214155 : adjusted-palette-charcoal
# 8FBF97 : adjusted-palette-dark-sea-green
# 387D7A : adjusted-palette-myrtle-green
# AACDC4 : adjusted-palette-opal
# ABA9BF : adjusted-palette-wild-blue-yonder

### Slides Icon Colors
# 055e7c : icon-dark-bluegreen
# 0780A8 : icon-light-bluegreen
```

Other available tools for creating a color scheme include [Coolors](https://coolors.co/) or [color palettes in Matplotlib.](https://matplotlib.org/3.1.0/tutorials/colors/colormaps.html)

- Colors should be easy to discriminate.
- Colors should not cause problems for common colorblind users. Ideally, color use should be limited to when it is needed; defer to visually different lines (e.g., dotted, dashed) as these will still be distinct if printed in black and white.

### Other Schemes

Having other schemes related to positions, font hierarchy and font size helps with maintaining consistency too.

- Position: Within a plot and presentation slides, keep similar elements located in the same location.
- Hierarchy of fonts: Match font family to your main text. Keep font size similar for similar elements and maintain hierarchy (e.g., plot title = size 16, ticks = size 12)

## Additional Conventions

When plotting actual vs predicted values, use the following convention:

X-axis: Predicted values

Y-axis: Actual values
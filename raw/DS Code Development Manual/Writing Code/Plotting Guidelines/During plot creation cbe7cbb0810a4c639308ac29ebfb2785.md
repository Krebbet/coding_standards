# During plot creation

1. Figures should contain only information that is relevant to the point you are making (i.e. as simple as possible but no simpler)
    - a figure can be complicated and contain more than one idea, if it is precise and obvious - but each point may and probably will require additional plots to describe/show evidence
2. Use histograms, line plots, scatters and bar plots
3. Bar charts should not be used to plot the mean/median value - just use a box plot. Can be used in some cases to denote things like count etc - i.e. the filled bar actually signifies something.
4. Scatter plots should always come with contours and trend lines - otherwise they are so hard to see anything
5. Never use 2 axes to describe the same attribute e.g. if you have a word frequency bar chart with unique words on the x-axis and counts on the y-axis, don’t colour the bars different colours per unique word, use the colours for say particular groups of words - otherwise just leave them all one colour

During creation of each plot, emphasize the message and decrease the noise; keep the following in mind when creating each plot:

1. Clarity
2. Cleanliness

# Clarity

Always ensure that plot elements have characteristics that encourage easy plot evaluation.

Example:

![](During%20plot%20creation/Untitled.png)

```
# specify color to overwrite the seaborn countplot default of using different 
	#colors for different categories.
sns.countplot(df4.var1, color='#1f77b4')

#Specify a title and use a large fontsize
plt.title('Count Plot for Variable 1', fontsize = 16)

# Specify a human-readable (e.g., spell out "Variable 1" instead of just using "var1") 
	#for x and y labels and use a large fontsize.
  #Use hierachy for fontsize so it is smaller than the title font size.
plt.xlabel('Variable 1', fontsize = 14)
plt.ylabel('Count', fontsize = 14)
plt.yticks(fontsize = 12)
plt.show()
```

## High resolution

Plots should be high resolution. As poor resolution quality can be less obvious on smaller screens or some screen types, please check this by zooming in or expanding the plot.

- When plotting in Jupyter notebooks, use:
    
    ```
    %config InlineBackend.figure_format = 'retina'
    ```
    
- When saving plots, remember to specify resolution to be at least 300dpi:
    
    ```
    plt.savefig('figure_name.png' , bbox_inches='tight', dpi=300)
    ```
    
- Ensure that equations are also in high resolution. A useful tool for creating them is Sciweaver's [online latex equation editor](http://www.sciweavers.org/free-online-latex-equation-editor).

## Optimal plot type

- Histograms, line plots, scatter plots, and bar plots are generally great options for plots.
    - In scatter plots, add contours and trend lines for viewing ease when presenting to technical audiences.
- 3d plots and pie charts are generally poor options for plots because they often use more than one element to describe the same attribute (increases visual complexity; see [here](https://www.notion.so/Plotting-Guidelines-46b8cd2fda0544ccb9cc86a8e28a06d2?pvs=21))
- Note that when plotting the mean/median values, bar charts should *not* be used. Instead, use a box plot
    - Bar charts can be used in some cases to denote things like count etc (i.e. situations where the filled bar actually signifies something).

## Encourage Easy Information Extraction

- Font size should allow easy information extraction at a glance, even on a smaller screen or afar. Consider where the plot will be used, as the appropriate font size for a report is different from that in a presentation.
- Do not employ more than one element (axis, hue, etc) to describe the same attribute
    
    The top plot uses both the axis and color to describe distinguish the value of `var1`. Using more than one element is unnecessary, and makes it difficult to evaluate the plot especially when more information is contained in the plot. 
    
    ![](During%20plot%20creation/eg1.png)
    
    Instead, use only one element to describe the single attribute:
    
    ![](During%20plot%20creation/eg2.png)
    
- Consider the optimal aspect ratio for your needs
    - Square images allow for text explanations on the sides
    - Wide images allow for text captions on the top/bottom; tall images generally don’t fit well on slides/tv screens.
    - Wide images will often work best in report layouts as well

### Include Critical Plot Elements

Critical elements that should be included in every plot:

- [ ]  Title OR caption: All exhibit should have concise but descriptive captions, such that the exhibit content can be digested independently with a quick glance at the caption.
    - The main description of each exhibit should be reserved for the main text.
    - Captions that are two or more lines should be left aligned rather than center aligned.
- [ ]  Labels for the x and y axes
- [ ]  Tick labels (with units if necessary)
- [ ]  A legend (if relevant) with legend labels

# Cleanliness

Facilitate user comprehension by minimizing unnecessary elements that can distract viewers from the main message. This can be done by improving the signal-to-noise ratio of the plot. 

## Create White Space

Use a white background and use other tricks for white space.

- Leave out complicated grey, etc backgrounds.
- Grid lines should generally be avoided unless they noticeably assist with plot evaluation.

## Emphasize Differences

- Use hierarchy (color, styling, etc) to differentiate title vs content.
- Be careful about color scales (complicated color scales can obscure the message).
    
    The top correlation plot has a complicated color scale that does not aid in understanding which pairs are more highly correlated
    
    ![](During%20plot%20creation/pal1.png)
    
    It is generally better to use a continuous color scale that displays how attributes are different from each other. Color scales generally work best if they differ in both hue AND saturation. This allows varying levels of the scale to be easily distinguished even when they are printed greyscale or if common forms of colorblindness are present.
    
    ![](During%20plot%20creation/pal2.png)
    
    - When the values are all on the same spectrum (e.g., all positive) like in the example plot above, you can use a similar color scheme that ranges in intensity (i.e., in this case on a continuous warm spectrum from white/largest to deep purple/smallest).
    - When values range from negative values to positive values, consider a diverging palette like `sns.palplot(sns.color_palette("RdBu_r", 7))` and align white at zero. However, be careful since diverging color palettes can have difficulty in differentiation when they are converted to greyscale or other forms of colorblindness are in effect:
        
        ![](During%20plot%20creation/pal_RdBu_r.png)
        

## Cleanliness In Tables

While not a type of plot, the cleanliness principal can also be applied to tables:

- Provide sufficient white space/padding for each cell.
- When possible, use white space instead of line dividers.
    - Google slides naturally restricts tables to include a lot of white space, so this should be easy to accomplish.
- Consider alignment (left, center, and right) within the cells.
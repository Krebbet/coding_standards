# Function, Comment, and Argument Lengths

Last Edit: April 5, 2023 6:44 PM
Level: Variable

## Restrict Lengths / Quantities of Functions, Comments, and Arguments

In general functions and classes should do only 1 task and be no more than 10-20 lines of code. 

Functions and classes that are longer should be broken up and grouped into sub-components that are modular. 

Not only does this make the code less complicated and easier to debug, this is also important for unit-testing (see [here](https://www.notion.so/4481ab0262774980871785a277ffa4ff?pvs=21)).

This concept of keeping code minimal should be extended to all things, specifically:

- Comments should be as short as possible but no shorter. Do NOT include information about how the code fits in the framework that is *not relevant* to the particular code in the script.
- Number of arguments can/should be grouped into data structures e.g. dictionaries or class of configs that group into common themes.

**Bad:**

```python
def run_code(train_generator, valid_generator):
    """
			...
		"""
    logger.info("Creating the base model ...")
    base_model = EfficientNetB0(
        include_top=False,
        weights="imagenet",
    )

    # add global spatial pooling layer #MLFIX this comment is redundant but should be replaced with WHY this is being done
    logger.info("Creating the full model ...")
    x = base_model.output
    x = GlobalAveragePooling2D()(x)

    # add logistic layer for classification
    predictions = Dense(len(train_generator.class_indices), activation="softmax")(x)

    # setup full model
    model = Model(inputs=base_model.input, outputs=predictions)

    # step 1: train top layers by freezing base model
    for layer in base_model.layers:
        layer.trainable = False

    # compile and train model
    logger.info("Training top model layers ...")
    model.compile(
        optimizer=Adam(learning_rate=LEARNING_RATE_TOP_LAYERS),
        ...
    )

    model.fit(
        x=train_generator,
        ...
        workers=WORKERS,
    )

    # step 2: train all model layers
    for layer in model.layers:
        layer.trainable = True

    # re-compile and train model
    logger.info("Training all model layers ...")
    model.compile(
        optimizer=Adam(learning_rate=LEARNING_RATE_ALL_LAYERS),
        ...
    )
    model.fit(
        x=train_generator,
        ...
        workers=WORKERS,
    )
```

**Good:**

```python

def run_code(...)
	model = setup_base_model("EfficientNetB0") 

	model = train_top_layers(model, number_top_layers) # modular and reusable code

	model = train_all_layers(model, params)

	...

def setup_base_model(model_name):
	...
	return model
```
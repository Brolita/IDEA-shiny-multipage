# Multipage Shiny

## Explanation

`createMultipageServer` will return the configurations of a multipage shiny application. Each person who connects to the shiny app will be prompted to pick a viewer. By default there is always a "Controller" viewer, which contains the inputs which will effect the other viewers. There can be as many viewers as you wish to define. 

## Usage

### Call:

```
settings = createMultipageServer(
    list(
        ...
    ),
    ...
)

shinyApp(ui = settings$ui, server = settings$server)
```

### Definition:

```
createMultipageServer <- function(
    controller,
    ...
)
```

`Controller` is a list of shiny inputs. See the [Shiny Documentation](https://shiny.rstudio.com/reference/shiny/latest/) for a list of existing inputs. 

Additional arguments are functions which return `plotly` objects. The name of the argument will be used as the "viewer name." Each function takes a "shiny input", which behaves as a normal reactive input object. See [an example of reactive inputs from the Shiny Docs](https://shiny.rstudio.com/articles/build.html). *Please note:* You do NOT have to call a render method. Simply return the plot.

## Example

The following example is a simple example of an app.

```
library(plotly)
library(shiny)

source("multipage.R")

settings = createMultipageServer(
    list(
        selectInput(inputId = "example",
            label = "Make this appear in other view:",
            choices = c('Choice 1', 'Choice 2'),
            selected = 'Choice 1')
    ),
    Viewer = function(input) {
        return(plot_ly() %>% layout(title=input$example))
    }
)

shinyApp(ui = settings$ui, server = settings$server)
```

This somewhat crude example should give you an idea of how to create a full scale product.

## Future work

### Bugs

* Currently, once a server is launched, the `controlledInput` object is not initialized to have the correct initial values. Once a controller has connected once, the bug subsides for as long as the server stays up

### Features

* There is currently no controller locking, to prevent multiple controllers. This means that multiple controller will "fight" to have the correct value, and can cause a "flickering" effect, as shiny attempts to take on the value of the controller which is different.

* Only plotly renders are supported, however the implentation could be reworked to allow the plot functions to call their own render methods. No research has been done on this end.

* Devs have no ability to add their own javascript as source. Might be a useful feature needed to support more complex input functions.

## Demo

The current demo for the campfire is [here](https://brolita.shinyapps.io/campfire/).

You can host your apps at [shinyapps.io](https://shinyapps.io/) for free! Though for full scale deployments, you will most likely need to host the server elsewhere.

# Load packages ----------------------------------------------------------------

library(shiny)
library(shinythemes)
library(ggplot2)
library(dplyr)
library(plotly)

# Load data --------------------------------------------------------------------

Songlists <- read.csv(file = "https://raw.githubusercontent.com/rpgabriela/RShiny_Songs_Data/main/Song%20List%20Database.csv", header = TRUE, sep = ",")

# Define UI --------------------------------------------------------------------

ui <- fluidPage(
  
  h2("BPM Database Viewer for S3729C"),
  h4(tags$a(href = "https://shiny.rstudio.com/", "Powered by R Shiny")),
  
  
  br(),
  
  sidebarLayout(
    sidebarPanel(
      selectInput(
        
        inputId = "y",
        label = "Y-axis:",
        choices = c("BPM","Danceability"),
        selected = "BPM"
      ),
      
      selectInput(
        inputId = "x",
        label = "X-axis:",
        choices = c("Duration", "Danceability"),
        selected = "Duration_S"
      ),
      
      selectInput(
        inputId = "z",
        label = "Color by:",
        choices = c(
          "Key" = "Key",
          "Artist" = "Artist",
          "SongTitle" = "SongTitle"),
        selected = "BPM"
      ),
      
      sliderInput(
        inputId = "alpha",
        label = "Alpha:",
        min = 0, max = 1,
        value = 0.5
      ),
      
      sliderInput(
        inputId = "size",
        label = "Size:",
        min = 0, max = 5,
        value = 2
      )
    ),
    
    # Output: Show scatterplot
    mainPanel(
      plotOutput(outputId = "scatterplot", brush = "plot_brush"),
      dataTableOutput(outputId = "Songlists"),
      br()
    )
  )
)

# Define server ----------------------------------------------------------------

server <- function(input, output, session) {
  
  output$scatterplot <- renderPlot({
  
    p = ggplot(data = Songlists, aes_string(x = input$x, y = input$y, 
                                        color = input$z)) +
      geom_point(alpha = input$alpha, size = input$size)
    
  
  output$Songlists<- renderDataTable({
    brushedPoints(Songlists, brush = input$plot_brush) %>%
      select(SongTitle, Artist, Key, BPM, Duration)
  })
  
  ggplotly(p)
  
  })
  
}
# Create a Shiny app object ----------------------------------------------------

shinyApp(ui = ui, server = server)

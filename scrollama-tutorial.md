# Scrollama Tutorial: Step-by-Step Scrollytelling

This tutorial shows how to create a scrollytelling experience using Scrollama.js. We'll build step-based scroll interactions that trigger content changes as users scroll through the page.

## Setup

First, include Scrollama in your HTML:

```html
<script src="https://unpkg.com/scrollama"></script>
```

## HTML Structure

Create the basic scrollytelling layout:

```html
<main>
    <!-- Intro section -->
    <section id='intro'>
        <h1 class='intro__hed'>Your Title</h1>
        <p>Scroll down to explore the content.</p>
    </section>
    
    <!-- Scrolly section with sticky visualization and steps -->
    <section id='scrolly'>
        <!-- Sticky figure (visualization area) -->
        <figure>
            <div id="chart"></div>
        </figure>
        
        <!-- Article with scroll steps -->
        <article>
            <div class='step' data-step='0'>
                <p>First step content goes here.</p>
            </div>
            <div class='step' data-step='1'>
                <p>Second step content goes here.</p>
            </div>
            <div class='step' data-step='2'>
                <p>Third step content goes here.</p>
            </div>
            <!-- Add more steps as needed -->
        </article>
    </section>
</main>
```

## CSS Styling

Add essential CSS for the scrollytelling layout:

```css
body {
    margin: 0;
    font-family: -apple-system, BlinkMacSystemFont, 'Segoe UI', Roboto, sans-serif;
    background-color: #fafafa;
}

#intro {
    max-width: 40rem;
    margin: 1rem auto;
    text-align: center;
    padding: 2rem;
}

#scrolly {
    position: relative;
    background-color: #f1f3f3;
    padding: 1rem;
}

/* Sticky figure for visualization */
figure {
    position: sticky;
    left: 0;
    width: 100%;
    margin: 0;
    transform: translate3d(0, 0, 0);
    background-color: #fafafa;
    z-index: 0;
}

/* Article containing steps */
article {
    position: relative;
    padding: 0;
    max-width: 20rem;
    margin: 0 auto;
}

/* Individual step styling */
.step {
    margin: 0 auto 2rem auto;
    padding: 1.5rem;
    border: 1px solid #333;
    color: #fff;
    background-color: #333;
    box-shadow: 2px 5px 2px rgba(0, 0, 0, .2);
}

.step:last-child {
    margin-bottom: 0;
}

/* Active step styling */
.step.is-active {
    background-color: goldenrod;
    color: #3b3b3b;
}

.step p {
    text-align: center;
    padding: 1rem;
    font-size: 1.1rem;
    margin: 0;
}

/* Desktop layout */
@media (min-width: 840px) {
    #scrolly {
        display: flex;
    }

    #scrolly > * {
        flex: 1;
    }

    figure {
        position: sticky;
        width: 60%;
        height: 100vh;
        top: 0;
    }

    #chart {
        height: 100vh;
        display: flex;
        align-items: center;
        justify-content: center;
    }

    article {
        width: 40%;
        max-width: none;
        padding: 0 1rem;
    }

    .step {
        height: 100vh;
        display: flex;
        align-items: center;
        justify-content: center;
    }
}
```

## Initialize Scrollama

```javascript
// Create scrollama instance
const scroller = scrollama();

// Setup scrollama configuration
scroller
    .setup({
        step: '.step',    // CSS selector for step elements
        offset: 0.5,      // Trigger point (0.5 = middle of viewport)
        debug: false      // Set to true for debugging
    })
    .onStepEnter(response => {
        // This function runs when a step enters the viewport
        const step = +response.element.dataset.step;
        
        // Update step styling
        d3.selectAll('.step').classed('is-active', false);
        d3.select(response.element).classed('is-active', true);
        
        // Call your step handler function
        handleStepEnter(step);
    })
    .onStepExit(response => {
        // Optional: This function runs when a step exits the viewport
        // const step = +response.element.dataset.step;
        // handleStepExit(step);
    });
```

## Step Handler Function

Create a function to handle what happens at each step:

```javascript
function handleStepEnter(step) {
    console.log('Entered step:', step);
    
    switch(step) {
        case 0:
            // First step actions
            updateVisualization('intro');
            break;
        case 1:
            // Second step actions
            updateVisualization('highlight-data1');
            break;
        case 2:
            // Third step actions
            updateVisualization('highlight-data2');
            break;
        case 3:
            // Fourth step actions
            updateVisualization('show-all');
            break;
        // Add more cases as needed
    }
}

function updateVisualization(action) {
    // Your visualization update logic goes here
    // This could update charts, highlight elements, animate transitions, etc.
    
    if (action === 'intro') {
        // Show overview
    } else if (action === 'highlight-data1') {
        // Highlight specific data
    }
    // ... more conditions
}
```

## Responsive Design

Ensure your scrollytelling works on mobile:

```javascript
// Handle window resize
window.addEventListener('resize', scroller.resize);
```

## Complete Example with D3 Integration

Here's how to integrate with a D3 visualization:

```javascript
// Wait for data to load, then initialize scrollama
d3.json('data.json').then(function(data) {
    // Create your D3 visualization first
    createVisualization(data);
    
    // Then initialize scrollama
    const scroller = scrollama();
    
    function highlightLine(step) {
        // Reset all lines
        d3.selectAll('.line-path').style('opacity', 0.2);
        
        switch(step) {
            case 0:
                d3.selectAll('.line-path').style('opacity', 0.6);
                break;
            case 1:
                d3.select('.line-pasta').style('opacity', 1);
                break;
            case 2:
                d3.select('.line-pork').style('opacity', 1);
                break;
            // Add more cases...
        }
    }
    
    scroller
        .setup({
            step: '.step',
            offset: 0.5,
            debug: false
        })
        .onStepEnter(response => {
            const step = +response.element.dataset.step;
            highlightLine(step);
            
            // Update step styling
            d3.selectAll('.step').classed('is-active', false);
            d3.select(response.element).classed('is-active', true);
        });
    
    // Initialize with first step
    highlightLine(0);
});
```

## Best Practices

1. **Data Attributes**: Use `data-step` attributes to identify steps uniquely
2. **CSS Classes**: Use `.is-active` class to style the current step
3. **Smooth Transitions**: Add CSS transitions for smooth visual changes
4. **Mobile Considerations**: Test on mobile devices and adjust layout accordingly
5. **Performance**: Debounce expensive operations in step handlers
6. **Accessibility**: Ensure content is accessible without JavaScript

## Key Scrollama Methods

- `.setup(options)`: Configure the scroller
- `.onStepEnter(callback)`: Handle step enter events
- `.onStepExit(callback)`: Handle step exit events
- `.resize()`: Recalculate trigger points after layout changes

## Configuration Options

- `step`: CSS selector for step elements
- `offset`: Trigger point (0 = top, 1 = bottom of viewport)
- `debug`: Show trigger lines for debugging
- `threshold`: Number of trigger points (default: 4)

This tutorial provides the foundation for creating engaging scrollytelling experiences that combine narrative text with interactive visualizations.
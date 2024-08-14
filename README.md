# Mapify Category Filter
How to filter by category, allowing the use of a single map with multiple instances

## 🚨The Problem
In order to get a map that displays a specific group of kiosks—such as a county—a new map instance was needed. This lead to more maintenance and room for errors when adding new locations.

## 🧑‍💻 The Solution
We can add categories to the locations we want to display. Using a bit of javascript, we can fill the `<select>` (dropdown filter) with the category of our choosing. As long as we get the timing right, it should load a map with just the category we want.

## How to set it up
Definitions:

- Map Tag: Mapify-specific categories for the map

---

1. Add the Map Tag to the map and enable filter
   - **Map tag**: MapifyPro > Maps > [DESIRED MAP] > Map Location Tags
   - **Enable filter**: MapifyPro > Maps > [DESIRED MAP] > Enable Filter List > *SET TO* Yes
2. Give the desired location the Map Tag
   - MapifyPro > Map Locations > [DESIRED LOCATION] > *HOVER* over location > *SELECT* Quick Edit > *TYPE* [MAP TAG] into the input and *CLICK* on the tag (the tag will show up once you start typing 😔)
   - This can be done using Mapify's batch upload. The import document needs to be formatted and shortcodes need generated. *See Mapify Import Generator repo*
3. OPTIONAL: Hide the filter dropdown `.mpfy-filter {display: none;}`
4. Get the post id for the Map Tag
   - MapifyPro > Location Tags > *CLICK* [DESIRED TAG] > Look in URL for `...ID=[NUMBER]...` and make a note of the `NUMBER`—you'll need it!
5. Navigate to the page where the map will be displayed
6. Open the page with BeaverBuilder and add the map shortcode to the page using the HTML/Paragraph module
7. Under Tools (top-left dropdown with Beaver illustration), select Layout CSS & Javscript
   - Alternatively, you can use CMD + Y while mouse focus is on BB editor pane
8. In the Javascript tab, paste the code below

``` javascript
window.addEventListener('DOMContentLoaded', () => {

const mapTagID = 000; // <-- Change the numbers (000) to the ID from step 4

setTimeout(function () {

    // Set the value of the original <select> element
    const selectElement = document.querySelector('select[name="mpfy_tag"]');
    selectElement.value = mapTagID;

    // Trigger the change event
    const event = new Event('change');
    selectElement.dispatchEvent(event);

    // Update the custom-styled select
    const selectedItem = document.querySelector('.selecter-item[data-value="' + selectElement.value + '"]');
    if (selectedItem) {
        // Remove the 'selected' class from the currently selected item
        const currentlySelected = document.querySelector('.selecter-item.selected');
        if (currentlySelected) {
            currentlySelected.classList.remove('selected');
        }

        // Add the 'selected' class to the new selected item
        selectedItem.classList.add('selected');

        // Update the visible text in the custom-styled element
        const selecterSelected = document.querySelector('.selecter-selected');
        if (selecterSelected) {
            selecterSelected.textContent = selectedItem.textContent;
        }
    }

    }, 150);
})
```

9.  Change the value of the `mapTagID` variable at the top of the code snippet to match the ID you made a note of from step 4 and publish the page
10. Adjust the `150` value at the bottom of the code snippet if needed
    -  That number is in milliseconds (1000 = 1 second) and tells the browser how long to wait before firing off the code. We have to wait a little bit for Mapify to render the map and pins.

        *I think it's a hard timer, so network speeds shouldn't affect it*
11. Go to the map config page in wp-admin and add the location tag to the map. It will not work without this!

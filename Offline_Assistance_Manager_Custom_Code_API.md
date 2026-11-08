# Offline Assistance Manager — Custom Code API

This reference describes the JavaScript functions available to Custom Code scripts.
The function list is generated from the same help entries shown in the app.

## Runtime environment

- Language: JavaScript.
- Scripts run in a restricted environment. They cannot directly access Android classes or arbitrary files.
- Screen coordinates are measured in pixels unless a function or resource setting explicitly says otherwise.
- Detection functions return result objects or arrays; check a result's found property before using its coordinates.
- Test Mode previews supported actions instead of dispatching real screen gestures.

## Built-in functions

Each entry includes the call signature and the description shown in the in-app help.

### `findImage(id)`

Return the first match for an image resource, or a not-found result.

### `findImageOnCapture(resourceId, captureId)`

Search the entire saved screenshot identified by grid.captureId. It uses the IMAGE resource's configured matching method and threshold. The saved frame stays available until the next findGrid() call or the script ends.

### `findImages(id, limit)`

Return up to limit matches for an image resource.

### `findMatches(id, limit)`

Alias for findImages(); returns a list of match rectangles.

### `findNearest(id, x, y, limit)`

Return the match whose centre is nearest to screen coordinate (x, y), with its distance.

### `countMatches(id, region)`

Count matches for an image resource. If region is supplied, count only matches whose centres fall inside it.

### `findRelative(anchor, imageIdOrMatches, relation, maxDistance)`

Find matches left, right, above, below, inside, overlapping, or nearest to anchor. The second argument can be a resource ID or match list; maxDistance is in pixels.

### `groupMatches(matches, maxDistance)`

Cluster matches by centre-to-centre distance and return each group's bounds and members.

### `findAligned(matches, direction, tolerance)`

Group match centres into horizontal rows, vertical columns, or diagonal lines using a pixel tolerance.

### `getBounds(matches)`

Return the combined bounds, size, centre, and count for matches or a grid/group result.

### `captureFrame()`

Capture and retain one immutable screen frame for level OCR and later grid matching.

### `getRegion(regionId)`

Return the configured screen-pixel bounds of a Region resource.

### `readTextFromRegion(regionId, captureId)`

Run OCR on a configured Region resource, optionally using the exact immutable frame returned by captureFrame().

### `scaleGridResources([imageIds], baseRows, baseColumns, rows, columns)`

Scale only the listed IMAGE resources from their Level 1 master tile size for the selected grid dimensions. Unlisted image resources are not changed.

### `findGrid(idOrIds, maxMatchesPerResource, gridId, captureId)`

Detect a complete rectangular grid from IMAGE resource IDs. When captureId is supplied, grid detection uses that exact saved frame.

### `findTextInRegion(region)`

Run bundled Latin-script OCR once on the current screen region and return recognised text lines with screen coordinates.

### `findTextInGrid(grid)`

Run bundled Latin-script OCR once on the whole screen frame for the grid, then assign recognised lines to cells by their centre coordinates. findGrid() uses its exact saved capture; no OCR is run separately for each cell.

### `searchGridText(gridOrGrids, query, options)`

Search existing OCR text from one text grid or an array of text grids without running OCR again. Returns matches with gridIndex, row, column, text and screen coordinates. Options: caseSensitive (default false), exactMatch (default false), maxResults (default 128, maximum 500).

### `readTextFromImage(imageId)`

Read Latin-script text from an IMAGE resource bitmap, using its original image coordinates.

### `findTextInImage(imageId)`

Alias of readTextFromImage(); returns the recognised text and line coordinates.

### `lookForText(text)`

Search the current screen for a word or phrase using case-insensitive matching. Returns matching OCR lines and their coordinates.

### `lookForTextInRegion(text, region)`

Search one screen region for a word or phrase with one OCR pass and case-insensitive matching.

### `lookForTextInGrid(text, grid)`

Search a whole grid in one OCR pass, then return case-insensitive matching text grouped by cell.

### `lookForTextInImage(imageId, text)`

Search an IMAGE resource for a word or phrase using one OCR pass and case-insensitive matching.

### `parallelWork({ workers, tasks })`

Run each function in tasks concurrently, up to workers at a time, and return results in the same order. Nested calls may request additional workers; all calls reuse the shared worker system.

### `parallelGridWork({ workers, tasks, cellTasks })`

Run each tasks function once and each cellTasks function once for every cell in the most recent grid. A cell function receives (cell, index). Results are returned for each task and cell.

### `givePriority(level)`

Set the default gesture/action priority for the rest of this custom-code execution. 0 = background (drops actions while busy), 1 = main automation, and 2 or higher = higher than main. This does not change matching-worker scheduling.

### `giveActionPriority(level, functionOrFunctions)`

Run one function or an array of functions in order with a temporary gesture/action priority. Nested parallel work inherits the temporary priority.

### `traceImages(id, limit, shapeId)`

Return only actual detected image matches with row/column indices; missing positions are not filled. Optional shapeId names the result for later calls.

### `findShape(id, limit, shapeId)`

Backwards-compatible alias for traceImages(); existing scripts continue to work.

### `createShape(type, x, y, width, height, shapeId)`

Create a named geometric outline centred at (x, y). Supports circle, square, rectangle, triangle, and ellipse; width and height are screen pixels.

### `moveShape(shapeId, x, y)`

Move the saved shape geometry so its centre becomes (x, y). This changes stored coordinates only; it does not send a screen gesture.

### `offsetShape(shapeId, dx, dy)`

Move the saved shape geometry by dx and dy pixels from its current centre. This changes stored coordinates only; it does not send a screen gesture.

### `setShapeBoundaryRestriction(restrictedIds, boundaryIds)`

Prevent each named restricted shape from crossing any named boundary shape. Pass two arrays of shape IDs; use [], [] to clear the rules.

### `findCells(id, limit, gridId)`

Return detected cells as a list with zero-based row and column values. Optional gridId names the parent grid.

### `createGrid(left, top, right, bottom, rows, columns, gridId)`

Divide a screen rectangle into evenly sized rows and columns. Coordinates are screen pixels; optional gridId names the grid.

### `getGridCell(grid, row, column)`

Return the cell object at a zero-based row and column. Kept for compatibility with existing scripts.

### `getGridCellData(grid, row, column)`

Return the complete cell object, including bounds, coordinates and any OCR text/line metadata. Alias of getGridCell().

### `fillGridGaps(grid)`

Adjust adjacent cell boundaries to meet at the midpoint of each horizontal and vertical gap. The last row and column keep their outer edges. Updates and returns the same grid.

### `scaleGridCells(grid, scaleX, scaleY)`

Resize every cell around its centre. 1.2 makes that dimension 20% larger; 0.8 makes it 20% smaller. Updates and returns the same grid.

### `expandGridCells(grid, options)`

Extend cell edges by fixed pixels or percentages of each cell's original width/height. Supports left, top, right, bottom and their Percent variants, for example {bottomPercent: 25, lastRowOnly: true}. You can also target row/column numbers or use row: "last". Updates and returns the same grid.

### `clickGridCell(grid, row, column)`

Tap the centre of any cell region in the grid.

### `tapCell(row, column)`

Tap a cell in the latest grid using zero-based row and column indices.

### `tapCell(gridId, row, column)`

Tap a cell in a named grid using zero-based row and column indices.

### `drawGrid(grid)`

Draw outlines and row/column labels for all cells in a grid.

### `isPreviewMode()`

Return true during Test Mode, when tap, hold, swipe, drawPath, and drawShape calls only show previews.

### `removeDrawnGrid()`

Remove the grid-outline overlay.

### `findImageInGridCell(id, grid, row, column)`

Search an image resource strictly inside one cell from createGrid() or the complete grid returned by findGrid().

### `findImageAroundGridCell(id, grid, row, column)`

Search the eight outer parts of a cell using a 3-by-3 split and return matches with their positions.

### `findImageInRegion(id, left, top, right, bottom)`

Search for image matches whose centres lie inside the specified screen rectangle.

### `findImageAt(id, x, y, radius)`

Search for an image match within radius pixels of screen coordinate (x, y).

### `findModel(id)`

Run the ML model configured for resource id.

### `findResource(id)`

Run image or ML matching according to the resource's configured type.

### `getClickPoint(id)`

Return the saved X/Y coordinate from a Coordinate Click resource, or found=false if it is not configured.

### `clickResource(id)`

Tap the saved screen coordinate in a Coordinate Click resource.

### `getSwipePath(id)`

Return the saved points from a Swipe Path resource as an array of {x, y} objects.

### `swipeResource(id, durationMs)`

Perform the saved Swipe Path gesture. The optional per-segment duration overrides the resource setting; 50–1,000 ms.

### `getResourceColor(id[, left, top, right, bottom])`

Return a representative #RRGGBB colour from an IMAGE resource. Optional bounds use resource-image pixels, with right and bottom exclusive; fully transparent pixels are ignored.

### `getResourcePalette(id[, maxColors])`

Return up to 64 frequent visible RGB shades sampled across an IMAGE resource. Transparent pixels are ignored; defaults to 64 colours.

### `waitForImage(id, timeoutMs, pollMs)`

Wait until an image resource is detected or the timeout expires. Timeout is at most 10 seconds.

### `waitForNoImage(id, timeoutMs, pollMs)`

Wait until an image resource is no longer detected or the timeout expires. Timeout is at most 10 seconds.

### `waitForImageCount(id, minCount, timeoutMs, pollMs)`

Wait until at least minCount matches are detected. Timeout is at most 10 seconds.

### `waitForRegionChange(left, top, right, bottom, timeoutMs, pollMs, minChangedRatio)`

Wait for sampled pixels in the rectangle to change. It samples up to 16-by-16 points and defaults to a 5% changed-sample ratio.

### `waitForChange(region, timeoutMs)`

Wait for sampled pixels in a region object (left, top, right, bottom) to change. Default timeout is 3 seconds, capped at 10 seconds.

### `waitForColor(color, region, timeoutMs, tolerance)`

Wait until a sampled pixel in region matches #RRGGBB within the per-channel tolerance. Timeout is at most 10 seconds.

### `waitForScreenStable(timeoutMs, stableChecks)`

Wait for consecutive 16-by-16 screen samples to become similar. Timeout is at most 10 seconds.

### `screenWidth()`

Return the width of the current screen capture in pixels.

### `screenHeight()`

Return the height of the current screen capture in pixels.

### `refreshScreen()`

Request the most recent available screen capture frame.

### `captureScreen()`

Immediately request a fresh screen capture, bypassing the shared-frame cache.

### `getPixelColor(x, y)`

Return the pixel colour as #RRGGBB, or an empty string when outside the screen.

### `isColorAt(x, y, color, tolerance)`

Check whether a pixel matches #RRGGBB or 0xRRGGBB within a per-channel tolerance of 0 to 255.

### `colorRatioInRegion(left, top, right, bottom, color, tolerance)`

Return the estimated fraction of sampled pixels matching the colour.

### `findColorInRegion(left, top, right, bottom, color, tolerance)`

Return the first sampled pixel matching the colour, including found, x, y, and colour.

### `captureImage(key, left, top, right, bottom)`

Save a screen rectangle under key for comparison later in this run.

### `getPixelColorOnCapture(captureId, x, y)`

Read a pixel from the exact immutable screen frame returned by findGrid().

### `captureImageOnCapture(key, captureId, left, top, right, bottom)`

Save a crop from that same immutable findGrid() frame, so evidence images show the pixels used for detection.

### `cropScreen(region)`

Save a crop of a region object and return its key and bounds for compareImages().

### `compareImages(keyA, keyB, threshold)`

Compare two saved crops of equal dimensions.

### `getState(key)`

Read a text value stored for this automation run.

### `setState(key, value)`

Store a text value for later scripts in this automation run.

### `clearState(prefix)`

Delete stored state values, optionally limited to keys starting with prefix.

### `createTextField(options)`

Create an editable overlay field and open the Android keyboard. Options: x/y in screen pixels, width/height in dp, placeholder, initial value, maxLength, multiline, visible and enabled. Returns a handle with id and created. Up to four fields may exist per script execution.

### `waitForTextFieldSubmit(field, timeoutMs)`

Wait for the user to press Search/Done on the keyboard or the Search button. Returns submitted, value, timedOut and cancelled. Timeout defaults to 30 seconds and is capped at 180 seconds.

### `getTextFieldValue(field)`

Read the field's current text without waiting for submission.

### `updateTextField(field, options)`

Update the value, placeholder, position, size, visibility or enabled state. Options not supplied keep their current values.

### `removeTextField(field)`

Remove one editable field. All remaining fields are automatically removed when the script ends.

### `createButton(options)`

Create a touchable overlay button. x/y are screen pixels; optional width/height are dp (default 160×56 dp). Set draggable: true to let the user move the button; a drag updates the stored x/y position and a short tap still activates it. Colours accept #RRGGBB or #AARRGGBB. Use iconResourceId with iconPosition (left, right, top, bottom, only) and iconSize (dp) for an IMAGE resource. Optional pressedIconResourceId and disabledIconResourceId select IMAGE resources for those button states. Returns a handle with id and created. Up to 16 buttons may exist per script execution.

### `updateButton(button, options)`

Update an existing button. Only supplied fields change; omitted fields keep their current values. Set draggable to enable or disable dragging.

### `waitForButtonPress(button, timeoutMs)`

Wait for a physical press on the button overlay. Returns pressed, timedOut and cancelled. The timeout defaults to 30 seconds and is capped at 180 seconds.

### `getButtonState(button)`

Return whether the button exists, is visible, enabled and currently pressed, plus its current x/y screen position.

### `removeButton(button)`

Remove one button. Every remaining button is automatically removed when the script ends.

### `monitorUserInteraction(grid, timeoutMs)`

Wait for the next accessibility-reported tap or scroll event. Pass a grid object or named grid ID to map a tap into zero-based row and column values. For taps, x/y are the centre of the clicked accessibility node, not the exact finger position. Scroll events include direction and deltaX/deltaY. The function observes `TYPE_VIEW_CLICKED` and `TYPE_VIEW_SCROLLED` events while the call is active; it does not intercept touch input or enable touch-exploration mode. A launcher or app that does not expose page swipes as accessibility scroll events cannot be monitored this way. Timeout defaults to 30 seconds and is capped at 180 seconds. Returns `found`, `type`, `direction`, `x`, `y`, `row`, `column`, `cellFound`, `timedOut` and `cancelled`.

### `click(x, y, count, intervalMs)`

Tap screen coordinate (x, y) one or more times. Count is 1 to 20; interval is capped at 500 ms.

### `tap(target)`

Tap the centre of a detected match returned by a detection function.

### `clickMatchCenter(match)`

Tap the centre of a match object.

### `clickOffset(match, dx, dy)`

Tap at dx and dy pixels from the match centre.

### `swipe(x1, y1, x2, y2, durationMs)`

Swipe from the first screen coordinate to the second.

### `swipePath(points, durationMs)`

Perform straight swipe segments between adjacent points in the path.

### `hold(x, y, durationMs)`

Keep a stationary press at screen coordinate (x, y) for the requested duration (50–10,000 ms).

### `drawPath(pathId, points, durationMs)`

Draw and save a named path using 2–128 screen coordinates as one continuous gesture. Repeat the first point at the end to close it. Duration defaults to 1,000 ms.

### `drawShape(shapeId, durationMs)`

Draw a previously created geometric shape as one continuous closed gesture. Duration defaults to 1,000 ms.

### `dragShape(shapeId, targetX, targetY, durationMs)`

Drag the screen object represented by a named shape towards a new centre. A configured boundary stops the drag before the shape crosses it; duration defaults to 1,000 ms.

### `tapSequence(points, delayMs)`

Perform up to 128 taps in order. The total delay between taps is capped at 10 seconds.

### `wait(ms)`

Pause execution for up to 10 seconds.

### `log(message)`

Write a diagnostic message to the local app log.

### `recordActionCard(title, details)`

Add a text-only note to live run history.

### `recordActionCard(title, details, result)`

Add a note to live run history and attach an annotated image when result contains screen coordinates.

### `recordImageGrids(title, details, beforeKeys, afterKeys, rows, columns)`

Attach numbered before/after grids made from saved cell crops to the current custom-code action card.

### `recordPoleEvidence(title, details, tiles)`

Attach one 3x3 pole-evidence image per tile to the existing Custom Code action card. Each tile supplies four named pole crops and true/false results.

### `setVisualResultMode("grids_only")`

For this execution, save grid results but skip individual cell/crop images.

### `findImageInCell(resourceId, grid, row, column)`

Search only within the specified cell. The matched image must fit completely inside the cell bounds.

### `findImageInGridCell(resourceId, grid, row, column)`

Alias of findImageInCell; restricts both the search region and full match bounds to that cell.

## Resources

Resources are configured in the script's Resources tab and are referenced in JavaScript by their resource ID, usually as a string:

```javascript
const target = findImage("targetIcon");
if (target.found) clickMatchCenter(target);
```

Resource IDs must be unique within a script. The editor validates literal resource references and checks that the resource type is compatible with the function. Importing JavaScript code does not automatically import or configure its resources.

### Resource types

#### IMAGE (`image`)

An image template used by image-matching and image-analysis functions. Upload an image or create one from a screen crop. Each IMAGE resource keeps its own matching method and detection threshold.

Common interactions: findImage(id), findImages(id, limit), findGrid(idOrIds, ...), findImageInRegion(...), getResourceColor(id), and getResourcePalette(id). IMAGE resources can also supply button icons through iconResourceId, pressedIconResourceId, and disabledIconResourceId.

For screens whose tile dimensions change, scaleGridResources([imageIds], baseRows, baseColumns, rows, columns) scales only the IMAGE resources explicitly listed. Other images are left unchanged.

```javascript
const match = findImage("startButton");
if (match.found) clickMatchCenter(match);
```

#### ML_MODEL (`ml_model`)

A saved model selected from the app's ML Model library. The resource stores the selected model ID and name, along with its configured detection threshold. It is not an uploaded image template.

Use findModel(id) to run the selected model. findResource(id) can select image or model matching according to the resource's configured type.

```javascript
const detection = findModel("objectDetector");
if (detection.found) log("Model detection found");
```

#### REGION (`region`)

A rectangular screen area defined by left, top, right, and bottom coordinates in screen pixels. All four bounds must be configured, with right greater than left and bottom greater than top.

Use getRegion(id) to obtain its bounds, readTextFromRegion(id, captureId) to run OCR on the configured region, or use the returned region object with region-based text-search functions.

```javascript
const area = getRegion("titleArea");
if (area.found) {
  const text = findTextInRegion(area);
  log(text.text);
}
```

#### COORDINATE_CLICK (`coordinate_click`)

A saved X/Y screen coordinate in pixels. Both coordinates must be configured and non-negative.

getClickPoint(id) returns the saved point and a found flag. clickResource(id) performs a tap at that point.

```javascript
const point = getClickPoint("continueButton");
if (point.found) clickResource("continueButton");
```

#### SWIPE_PATH (`swipe_path`)

A saved screen-coordinate path containing 2–64 X/Y points. The resource also stores a per-segment duration from 50 to 1,000 milliseconds.

getSwipePath(id) returns its points as an array of {x, y} objects. swipeResource(id, durationMs) performs the saved path; the optional duration overrides the resource setting.

```javascript
const path = getSwipePath("dragPath");
if (path.found) swipeResource("dragPath", 300);
```

### How resources are connected to scripts and automations

- A resource belongs to one saved Custom Code script. Its ID is the name used by JavaScript calls.
- The resource ID is resolved against the resources attached to the script being executed; IDs are not global across all scripts.
- Automation templates reference the saved script rather than maintaining another copy of its source and resources.
- When a resource is renamed, the editor updates supported literal references in the script. Review dynamic or computed IDs separately.
- Removing a resource can leave code references unresolved. Run Check code after changing resource definitions.

### Resource type and function relationships

| Resource type | Main interactions |
| --- | --- |
| IMAGE | Image matching, OCR on an image, colour sampling, grids, and button icons |
| ML_MODEL | Model inference through findModel() or findResource() |
| REGION | Region bounds and screen OCR |
| COORDINATE_CLICK | Read or tap a saved screen coordinate |
| SWIPE_PATH | Read or perform a saved multi-point swipe |

## Example: using different resource types together

```javascript
const titleArea = getRegion("titleArea");
const titleText = titleArea.found ? findTextInRegion(titleArea) : null;

const start = findImage("startButton");
if (start.found) {
  clickMatchCenter(start);
} else {
  const fallback = getClickPoint("startFallback");
  if (fallback.found) clickResource("startFallback");
}
```

The examples illustrate the resource/function relationship. Configure the IDs and resource data in the script before running them.

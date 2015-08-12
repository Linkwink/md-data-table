# Material Design Data Table

This module is an effort to implement Material Design data tables in [Angular Material](https://material.angularjs.org/latest/#/). I hope that this module will serve as a temporary solution to those who need this functionality and also serve as a playground, or lessons learned, when developing an official solution.

Specification for Material Design data tables can be found [here](http://www.google.com/design/spec/components/data-tables.html).

* [Demo](#demo)
* [License](#license)
* [Installation](#installation)
* [Usage](#usage)
* [Change Log](#change-log)
* [API Documentation](#api-documentation)
* [Contributing] (#contributing)

## Demo

http://danielnagy.me/md-data-table

Here is a fork-able [Codepen](http://codepen.io/anon/pen/EjOobZ?editors=101) of the demo application. Please use this to reproduce any issues you may be experiencing.

## License

This software is provided free of change and without restriction under the [MIT License](LICENSE.md)

## Installation

This package is installable through the Bower package manager.

```
bower install angular-material-data-table --save
```

In your `index.html` file, include the data table module and style sheet.

```html
<!-- style sheet -->
<link href="bower_components/angular-material-data-table/dist/md-data-table.min.css" rel="stylesheet" type="text/css"/>
<!-- module -->
<script type="text/javascript" src="bower_components/angular-material-data-table/dist/md-data-table.min.js"></script>
```

Include the `md.data.table` module as a dependency in your application.

```javascript
angular.module('myApp', ['md.data.table', 'ngMaterial']);
```

> Tested in IE 10 and on IOS 7.

## Usage

**controller**

```javascript
angular.module('nutritionApp').controller('nutritionController', ['$nutrition', '$scope', function ($nutrition, $scope) {
  'use strict';
  
  $scope.selected = [];
  
  $scope.query = {
    filter: '',
    order: 'name',
    limit: 5,
    page: 1
  };
  
  function success(desserts) {
    $scope.desserts = desserts;
  }
  
  // in the future we may see a few built in alternate headers but in the mean time
  // you can implement your own search header and do something like
  $scope.search = function (predicate) {
    $scope.filter = predicate;
    $scope.deferred = $nutrition.desserts.get($scope.query, success).$promise;
  };
  
  $scope.onOrderChange = function (order) {
    return $nutrition.desserts.get($scope.query, success).$promise; 
  };
  
  $scope.onPaginationChange = function (page, limit) {
    return $nutrition.desserts.get($scope.query, success).$promise; 
  };

}]);
```

**markup**

```html
<md-data-table-toolbar>
  <h2 class="md-title">Nutrition</h2>
</md-data-table-toolbar>

<md-data-table-container>
  <table md-data-table md-row-select="selected" md-progress="deferred">
    <thead md-order="query.order" md-trigger="onOrderChange">
      <tr>
        <th name="Dessert" unit="100g serving" order-by="name"></th>
        <th numeric name="Calories" order-by="calories.value"></th>
        <th numeric name="Fat" unit="g" order-by="fat.value"></th>
        <th numeric name="Carbs" unit="g" order-by="carbs.value"></th>
        <th numeric name="Protein" unit="g" order-by="protein.value"></th>
        <th numeric name="Sodium" unit="mg" order-by="sodium.value"></th>
        <th numeric name="Calcium" unit="%" order-by="calcium.value"></th>
        <th numeric name="Iron" unit="%" order-by="iron.value"></th>
      </tr>
    </thead>
    <tbody>
      <tr md-auto-select ng-repeat="dessert in desserts.data">
        <td>{{dessert.name}}</td>
        <td>{{dessert.calories.value}}</td>
        <td>{{dessert.fat.value | number: 1}}</td>
        <td>{{dessert.carbs.value}}</td>
        <td>{{dessert.protein.value | number: 1}}</td>
        <td>{{dessert.sodium.value}}</td>
        <td show-unit>{{dessert.calcium.value}}</td>
        <td show-unit>{{dessert.iron.value}}</td>
      </tr>
    </tbody>
  </table>
</md-data-table-container>

<md-data-table-pagination md-limit="query.limit" md-page="query.page" md-total="{{desserts.total}}" md-trigger="onPaginationChange"></md-data-table-pagination>

```

## Change Log

**Version 0.8.3** August 11, 2015

* Inline menus have been updated for Angular Material 0.10.1

**Note:** Menus are going to look bad in the Codepen because Google has not yet updated the CDN for Angular Material to version 0.10.1

**Version 0.8.2** August 9, 2015

* Support for [inline menus](#inline-menus)

#### Notes

I targeted the latest version of Angular Material which is currently v0.10.0. There have been changes to the template for the select element since this release, so if you are using a version of Angular Material greater than v0.10.0 you will notice inconsistencies in the styling of the element. I will fix this as soon as v0.10.1 is released.

**Version 0.8.1** August 9, 2015

* I am now using the `$interpolate` service to get the start and stop symbols

**Version 0.8.0**

#### Syntax Changes

* The name of a column is now placed in a `name` attribute. This decision was made do to the difficulty of transforming the template with interpolate strings and `ng-repeat`.
* The `unit` and `show-unit` attributes can be used regardless of weather or not the column in numeric (if you want).
* `md-trim-column-names` has been renamed to just `trim` and is now enabled individually for each column.
* The `md-auto-select` and `md-disable-select` attributes have been moved to the `tr` element within the `tbody` element.

#### Improvements

* You may now (properly) use `ng-repeat` and `ng-attr-*` on column headers.
* This version fixes issue #41.
* This version fixes issue #57.
* Trimming long column names is achieved cleanly with CSS and no longer uses `table-layout: fixed` or Javascript.

#### Issues

I have discovered an issue in Chrome's (and Opera's) web browser. This issue has existed for sometime and either no one has noticed it or no one has really cared. It appears Chrome has issues with properly rendering the table when the container is small enough to allow the table to scroll horizontally and the viewport is short enough that it can be scrolled vertically. This results in an undesirable laggy/rubber-band-ish effect when scrolling vertically for cells that meet one of the following criteria:

* The cell is positioned relative.
* The cell contains an `md-checkbox` element.
* The cell contains an `md-icon` element.

I have tested Safari, FireFox, Mobile Safari, and even IE 10 and was not able to reproduce this issue. I will open an issue for this momentarily. Please leave a comment if you have any ideas on how to fix this. If you know anyone who works for Google, make them fix it :stuck_out_tongue_closed_eyes:.

**Update**

~~It appears the latest version of Chrome (v44.0.2403.130) for OS X has resolved this issue, yay!~~

I spoke too soon. On my work machine, an older non-retina display Macbook Pro, this is not experienced; however, on my personal machine, a retina display Macbook Pro, this is still experienced. It is clear that not all OS X Chrome users are effected by this bug. This bug seems to be related to hardware in some way? My personal machine has an Intel Iris Pro GPU with 1536 MB of VRAM. Hardware is not really my thing. Maybe someone else with a retina display Macbook Pro can reproduce this issue?

**Version 0.7.6**

* **Important:** Pagination is now its own toolbar and should not be wrapped in a `md-data-table-toolbar` element.
* The pagination toolbar will now collapse into two separate toolbars on screens less than or equal to `600px` wide.

View the [archives](ARCHIVE.md) for a complete version history.

## API Documentation

* [Column Ordering](#column-ordering)
* [Inline Menus](#inline-menus)
* [Long Header Titles](#long-header-titles)
* [Numeric Columns](#numeric-columns)
* [Pagination](#pagination)
* [Row Selection](#row-selection)
* [Table Progress] (#table-progress)
* [Table Toolbars](#table-toolbars)

### Column Ordering

| Attribute       | Target    | Type       | Description |
| :-------------- | :-------- | :--------- | :---------- |
| `md-order`      | `<thead>` | `String`   | Two-way data binding order property. |
| `md-trigger`    | `<thead>` | `function` | Will execute when the order is changed, passing the order as a parameter. |
| `order-by`      | `<th>`    | `String`   | The value to sort on when the user clicks the column name. |
| `descend-first` | `<th>`    | `NULL`     | Tell the directive to first sort descending. |

The `mdOrder` attribute will be update when the user clicks a `<th>` cell to the value defined by the `order-by` attribute. The `mdOrder` attribute can be used in to do server-side sorting or client-side sorting.

If the function assigned to the `md-triger` attribute returns a promise, a loading indicator will be displayed.

> This directive does not support sorting of in-place data, i.e. data included directly in the markup, nor do I plan on supporting this.

##### Server Side Ordering

The provided usage example takes advantage of server-side sorting by submitting a query to the server.

##### Client Side Ordering

Just add an `orderBy:` property to the `ng-repeat` attribute that matches the `md-order` attribute.

```html
<md-data-table-container>
  <table md-data-table>
    <thead md-order="order">
      <!-- this cell will order by the name property -->
      <th order-by="name">Dessert (100g serving)</th>
      <!-- this cell will not change the order when clicked -->
      <th numeric>Calories</th>
    </thead>
    <tbody>
      <tr ng-repeat="dessert in desserts | orderBy: order"></tr>
    </tbody>
  </table>
</md-data-table-container>
```

### Inline Menus

Table cells support inline menus. To use an inline menu, place an `md-select` element inside a table cell.

**Example**

```html
<td>
  <md-select ng-model="dessert.type" placeholder="Other">
    <md-option ng-value="type" ng-repeat="type in getTypes()">{{type}}</md-option>
  </md-select>
</td>
```

Clicking anywhere in the cell will activate the menu. In addition, the row will not be selected when the cell is clicked if you have automatic row selection enabled.

### Long Column Headers

| Attribute | Target    | Type   | Description |
| :-------- | :-------- | :----- | :---------- |
| `trim`    | `<th>`    | `NULL` | trim long column names. |

When `trim` is place on a column header, the width of the column will be determined by the column body. If the width of the column header exceeds the width of the column body, it will be truncated with ellipsis.

I believe it is wise to restrict the minimum width of a column. By default column headers will enforce a minimum width of `60px`. This is a just a CSS property; however, you may overwrite it in your style sheet.

```css
table[md-data-table] > thead > tr > th > div.trim {
  min-width: 100px;
}
```

### Numeric Columns

Numeric columns align to the right of table cells. Column headers support the following attributes for numeric data.

##### Header Cells

| Attribute    | Target  | Type     | Description |
| :----------- | :------ | :------- | :---------- |
| `numeric`    | `<th>`  | `NULL`   | Informs the directive the column is numeric in nature. |
| `unit`       | `<th>`  | `String` | Specifies the unit. Providing a unit will automatically add the unit, wrapped in parenthesis, to the header cell. |

##### Body Cells

| Attribute   | Target  | Type     | Description |
| :---------- | :------ | :------- | :---------- |
| `show-unit` | `<td>`  | `NULL`   | Displays the unit in the body cell; `unit` must be specified on the header cell. |

You may use Angular's [number](https://docs.angularjs.org/api/ng/filter/number) filter on a body cell to set the decimal precision.

```html
<!-- 2 decimal places -->
<td>{{dessert.protein.value | number: 2}}</td>
```

### Pagination

To use pagination add a `md-data-table-pagination` element to the `md-data-table-toolbar`.

| Attribute       | Type       | Description |
| :---------------| :--------- | :---------- |
| `md-label`      | `Object`   | Change the pagination label. The default is 'Rows per page:'.|
| `md-limit`      | `Number`   | A row limit. |
| `md-page`       | `Number`   | Page number. |
| `md-total`      | `Number`   | Total number of items. |
| `md-row-select` | `Array`    | Row limit options. The default is `[5, 10, 15]` |
| `md-trigger`    | `function` | Will execute when the page or limit is changed, passing the page and limit as parameters. |

The `md-label` attribute has the following properties.

| Property | Type     | Description |
| :--------| :------- | :---------- |
| text     | `String` | The pagination label. |
| of       | `String` | The 'of' in 'x - y of z'. |

If the function assigned to the `md-triger` attribute returns a promise, a loading indicator will be displayed.

**Example: Client Side pagination using ngRepeat.**

```html
<tr ng-repeat="item in array | orderBy: myOrder | limitTo: myLimit: (page - 1) * myLimit">
```

### Row Selection

> Requires `ng-repeat`.

| Attribute           | Target    | Type    | Description |
| :------------------ | :-------- | :------ | :---------- |
| `md-row-select`     | `<table>` | `Array` | Two-way data binding of selected items |
| `md-auto-select`    | `<tr>`    | `NULL`  | allow row selection by clicking anywhere inside the row. |
| `md-disable-select` | `<tr>`    | `expression | function` | Conditionally disable row selection |

**Example: Disable all desserts with more than 400 calories.**

```html
<tr md-disable-select="dessert.calories.value > 4000"></tr>
<!-- or assuming isDisabled is defined in you controller -->
<tr md-disable-select="isDisabled(dessert)"></tr>
```

### Table Progress

| Attribute         | Target    | Type      | Description |
| :---------------- | :-------- | :-------- | :---------- |
| `md-progress`     | `<table>` | `promise` | The table will display a loading indicator until notified. |

A progress indicator can be displayed in a couple different ways. If the function you supply to the `md-trigger` attributes returns a promise, then a loading indicator will be automatically displayed whenever any events within the scope of the table are dispatched (i.e. reordering, pagination).

It's not unlikely that some events outside the scope of the table will effect its contents. For example, a search field that filters the table by sending a query to the server. To provide flexibility for such events, the table directive has a `md-progress` attribute that accepts a `promise` and will display a progress indicator until notified.

### Table Toolbars

Tables may be embedded within cards that offer navigation and data manipulation tools available at the top and bottom.

In general, use an `md-data-table-toolbar` for table toolbars, however; if you need to display information relative to a particular column in the table you may use use a `<tfoot>` element. For example, say you had a `calories.total` property that summed the total number of calories and you wanted to display that information directly beneath the Calories column.

```html
<tfoot>
  <tr>
    <td></td>
    </td><strong>Total:</strong> {{calories.total}}</td>
  </tr>
</tfoot>
```

Observe that Calories is the second column in the table. Therefore, we need to offset the first column with an empty cell. If you need to offset many columns you can use `<td colspan="${n}"></td>` where `n` is the number of columns to offset.

> Note that the directive is smart enough to insert an empty cell for the row selection column and that empty cells are not required after the last cell.

## Contributing

**Requires**

* node
* grunt-cli

This repository contains a demo application for developing features. As you make changes the application will live reload itself.

**update**

I noticed the nutrition app was an inconvenience for people trying to run the app locally and contribute. I have updated the demo application to remove the dependency for the nutrition app. This is also a good example of how you can take advantage of `ngRepeat` to easily achieve client side sorting and pagination.

##### Running the App Locally

Clone this repository to your local machine.

```
git clone https://github.com/daniel-nagy/md-data-table.git
cd md-data-table
```

Create a new branch for the issue you are working on.

```
git checkout -b my-issue
```

Install the package dependencies.

```
npm install
bower install
```

Run the application and visit `127.0.0.1:8000` in the browser.

```
grunt
```

Make your modifications and update the build.

```
grunt build
```

Create a pull request!

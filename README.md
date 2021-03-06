# Js-insert-event
Cross-browser node insert event without performance degradation for modern browsers

## Requirements
- JQuery (1.8.x or higher) or zepto

## Browser support

Several methods are used to provide cross-browser support:

- Insert animation event ([fastest](http://www.backalleycoder.com/2012/04/25/i-want-a-damnodeinserted/)): IE10+, Edge, Firefox 5+, Chrome 4+, Safari 4+, Opera 12.1+
- Mutation observer if available
- Timer: Others

## Usage
- Insert the script in the `<head>` tag or at the end of the `<body>`
- Add the class `js-insert-event` to any node and a bubbling `insert` event will be triggered when inserted in the DOM.

## Restriction
Animation events are not fired if the element is hidden using `display:none`, use `visibility: hidden; height: 0; width: 0` instead.

## Performance

DOM insertion triggers listener after:
- 2-30 ms

```html
<!DOCTYPE html>
<html>
  <head>
    <link rel="stylesheet" type="text/css" href="dist/js-insert-event.min.css">
  </head>
  <body>
    <ul class="items">
    </ul>
    <script src="https://code.jquery.com/jquery-1.12.4.min.js"></script>
    <script src="dist/js-insert-event.min.js"></script>
    <script>
	(function(){
	  var timings = [],
	      $items = $('.items');
	  
	  setInterval(createItem, 10);
	  function createItem(){
	      timings.push(new Date());
	      var $item =  $('<li>', {'class': 'js-insert-event js-item', 'data-id': timings.length-1}).text('item #' + timings.length);
	      $item.appendTo($items)[0].scrollIntoView();
	  }

	  $(document).on('insert', '.js-item', function(){
	      $(this).text($(this).text() + ' - ' + (new Date() - timings[$(this).data('id')]) + ' ms').css({color: 'blue'}); 
	  });
	})();
    </script>
  </body>
</html>

```
[View demo](https://jsfiddle.net/xire28/rw8vt5qf/)

## Example
```html
<!DOCTYPE html>
<html>
  <head>
    <link rel="stylesheet" type="text/css" href="dist/js-insert-event.min.css">
    <link rel="stylesheet" type="text/css" href="https://code.jquery.com/ui/1.12.1/themes/ui-darkness/jquery-ui.css">
  </head>
  <body>
    <div class="container">
      <input type="date" class="js-insert-event js-inline-datepicker"/>
    </div>
    <hr/>
    <button class="js-duplicate-datepickers">Duplicate</button>
    
    <script src="https://code.jquery.com/jquery-1.12.4.min.js"></script>
    <script src="https://code.jquery.com/ui/1.12.1/jquery-ui.min.js"></script>
    <script src="dist/js-insert-event.min.js"></script>
    <script>
      $(document).on('insert', '.js-inline-datepicker', function(){
        //Get instant feedback
	setTimeout(function(){
          $(this).css({color: 'blue'});
        }.bind(this));
      
        //Takes some time
        $('<div>').insertAfter(this).datepicker({altField: this, inline: true, dateFormat: 'yy-mm-dd'});
      });

      $(document).on('click', '.js-duplicate-datepickers', function(e){
        $('.js-inline-datepicker').clone().appendTo('.container');
        e.preventDefault();
      })
    </script>
  </body>
<html>
```
[View demo](https://jsfiddle.net/xire28/b8yok1nh/)

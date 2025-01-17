+++
title = "Modal Dialogs in Bootstrap"
template = "demo.html"
+++

Many CSS toolkits include styles (and Javascript) for creating modal dialog boxes. 
This example shows how to use HTMX to display dynamic dialog using Bootstrap, and how to 
trigger its animation styles in Javascript.

We start with a button that triggers the dialog, along with a DIV at the bottom of your 
markup where the dialog will be loaded:

```html
<button 
	hx-get="/modal" 
	hx-target="#modals-here" 
	hx-trigger="click"
	class="btn btn-primary"
	_="on htmx:afterOnLoad wait 10ms then add .show to #modal then add .show to #modal-backdrop">Open Modal</button>

<div id="modals-here"></div>
```

This button uses a `GET` request to `/modal` when this button is clicked.  The
contents of this file will be added to the DOM underneath the `#modals-here` DIV.

We're replacing Bootstrap's javascript widgets with a small bit of Hyperscript to provide
smooth animations when the dialog opens and closes.

Finally, the server responds with a slightly modified version of Bootstrap's standard modal

```html
<div id="modal-backdrop" class="modal-backdrop fade show" style="display:block;"></div>
<div id="modal" class="modal fade show" tabindex="-1" style="display:block;">
	<div class="modal-dialog modal-dialog-centered">
	  <div class="modal-content">
		<div class="modal-header">
		  <h5 class="modal-title">Modal title</h5>
		</div>
		<div class="modal-body">
		  <p>Modal body text goes here.</p>
		</div>
		<div class="modal-footer">
		  <button type="button" class="btn btn-secondary" onclick="closeModal()">Close</button>
		</div>
	  </div>
	</div>
  </div>
```

We're replacing the standard Bootstrap Javascript library with a little bit of Javascript, 
which triggers Bootstrap's smooth animations.

```javascript
function closeModal() {
	var container = document.getElementById("modals-here")
	var backdrop = document.getElementById("modal-backdrop")
	var modal = document.getElementById("modal")

	modal.classList.remove("show")
	backdrop.classList.remove("show")

	setTimeout(function() {
		container.removeChild(backdrop)
		container.removeChild(modal)
	}, 200)
}
```

<div id="modals-here"></div>

{{ demoenv() }}

<script src="https://unpkg.com/hyperscript.org"></script>

<style>
	@import "https://cdnjs.cloudflare.com/ajax/libs/twitter-bootstrap/4.5.2/css/bootstrap.min.css";
</style>

<script>
	function closeModal() {
		document.getElementById("modal").classList.remove("show")
		document.getElementById("modal-backdrop").classList.remove("show")

		setTimeout(function() {
			document.getElementById("modals-here").innerHTML = ""
		}, 200)
	}

	//=========================================================================
    // Fake Server Side Code
    //=========================================================================

    // routes
    init("/demo", function(request, params) {
		return `<button 
			hx-get="/modal" 
			hx-target="#modals-here" 
			bx-trigger="click"
			class="btn btn-primary"
			_="on htmx:afterOnLoad wait 10ms then add .show to #modal then add .show to #modal-backdrop">Open Modal</button>
	`})
		
	onGet("/modal", function(request, params){
	  return `<div id="modal-backdrop" class="modal-backdrop fade" style="display:block;"></div>
<div id="modal" class="modal fade" tabindex="-1" style="display:block;">
	<div class="modal-dialog modal-dialog-centered">
		<div class="modal-content">
			<div class="modal-header">
				<h5 class="modal-title">Modal title</h5>
			</div>
			<div class="modal-body">
				<p>Modal body text goes here.</p>
			</div>
			<div class="modal-footer">
				<button type="button" class="btn btn-secondary" onclick="closeModal()">Close</button>
			</div>
		</div>
	</div>
</div>`

});
</script>

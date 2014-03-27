jQuery.fn.disableTextSelect = function() {
	return this.each(function() {
		$(this).css({
			'MozUserSelect':'none',
			'webkitUserSelect':'none'
		}).attr('unselectable','on').bind('selectstart', function() {
			return false;
		});
	});
};

/*
-----------------
GLOBAL Util Module
-----------------
*/

function namespace(namespaceString) {
    var parts = namespaceString.split('.'),
        parent = window,
        currentPart = '';

    for(var i = 0, length = parts.length; i < length; i++) {
        currentPart = parts[i];
        parent[currentPart] = parent[currentPart] || {};
        parent = parent[currentPart];
    }

    return parent;
}

/*
-----------------
Module
-----------------

Name: Fill in Blank template
Description: A HTML5 activity template for a fill in the blank type activity.

*/
namespace("my.module.namespace");

my.module.namespace.DND = (function() {

    /********************************************************/
	/*                   DEPENDENCIES                       */
	/********************************************************/

		
	/********************************************************/
	/*                 PRIVATE MEMBERS                     */
	/********************************************************/
	// variable declaration 
	var __debug = true;
	var answers = [];
	var correctAnswer;
	var userAnswers = [];
	var question;
	
	var elementDragged=null;
	var elementDraggedOriginal=null;
	var elementDraggesdPinPos=null;

		
	/*
	 * Process HandleBars template with JSON
	 */
	function processLayoutWithContent(layoutHTML, contentJSON) {

		/* Compiling Template Using Handlebar*/
		var compiledTemplate = Handlebars.compile(layoutHTML);
		
		
		/*Compileing HTML from Template*/
		var compiledHTML = compiledTemplate(contentJSON);
		return compiledHTML;
	}
	


	/********************************************************/
	/*                 DOM EVENT HANDLERS                  */
	/********************************************************/	



	

	function embedDraggingCapablity(selector) {
		$.each($(selector),function(){
				constr(this);
				}
				);
	}
	
	function removeDraggingCapablity(selector) {
		$.each($(selector),function(){
				$(this).unbind('mousedown');
			}
		);
	}
	


	/********************************************************/
	/*                 ENGINE INIT FUNCTION                  */
	/********************************************************/

	function init(elRoot, params)	{
		$(elRoot).disableTextSelect();
		embedDraggingCapablity(".draggable:not(.dnd_disabled)");
	}

		function attachEvent(el, type, func, useCapture){
			$(el).bind(type,function(event) {
				func.apply(el, [event.originalEvent]);
				}
			);
		}
		
		
		function removeEvent(el, type){
				$(el).unbind(type);
		}
			
		function continueDragging(event){
			$(".selectedContainer").removeClass("selectedContainer");
			var container = findContainer(".droppable", elementDragged);
			$(container).addClass("selectedContainer");
			if(elementDragged==null) {
				return;
			}
			elementDragged.style.display="block";
			var posX = event.pageX - elementDraggesdPinPos.left;
			var posY = event.pageY - elementDraggesdPinPos.top;
			elementDragged.style.left = posX + "px";
			elementDragged.style.top = posY + "px";
		}
	
	function dropHandler(event) {
		elementDraggesdPinPos = null;
		if(elementDragged==null) {
			return;
		}
		
		var containerElement = findContainer(".droppable", elementDragged);
		elementDragged.parentElement.removeChild(elementDragged);		

		$(elementDragged).addClass("dnd_dropped");
		if($(containerElement).hasClass("droppable") && $(containerElement).children(".draggable").size()==0) {
			$(elementDragged).css("position", ""); // Need to remove the position attribute of dragged element
			$(elementDragged).width("100%");
			$(containerElement).append(elementDragged);
			removeDraggingCapablity(elementDragged);
			removeDraggingCapablity(".dnd_disabled");
			embedDraggingCapablity(".draggable.dnd_dropped");
		} else {
			$(elementDraggedOriginal).removeClass("dnd_disabled");
		}
		event.preventDefault();
		elementDragged = null;
	}
	
	
	function findContainer(containerSelector, elementDragged) {
		var overlapArea = 0;
		var container = null;
		var posElementDragged = {
			top : $(elementDragged).position().top,
			left : $(elementDragged).position().left,
			right: $(elementDragged).position().left + $(elementDragged).width(),
			bottom: $(elementDragged).position().top + $(elementDragged).height()
		}
		$.each($(containerSelector), function(){
			var tempArea = 0;
			var posContainer = {
				top : $(this).position().top,
				left : $(this).position().left,
				right: $(this).position().left + $(this).width(),
				bottom: $(this).position().top + $(this).height()
			}
			if(posContainer.top < posElementDragged.top) {	
				if(posContainer.bottom < posElementDragged.top) {
					return; // No overlapping area
				} else { // Some overlapping area
					if(posContainer.left < posElementDragged.left && posElementDragged.left < posContainer.right) { 
						tempArea = (posContainer.right - posElementDragged.left) * (posContainer.bottom - posElementDragged.top);
					} else if(posElementDragged.left < posContainer.left && posContainer.left < posElementDragged.right) {
						tempArea = (posElementDragged.right - posContainer.left) * (posContainer.bottom - posElementDragged.top);
					}
				}
			} else {
				if(posContainer.top > posElementDragged.bottom) {
					return; // No overlapping area
				} else { // Some overlapping area
					if(posContainer.left < posElementDragged.left && posElementDragged.left < posContainer.right) { 
						tempArea = (posContainer.right - posElementDragged.left) * (posElementDragged.bottom - posContainer.top);
					} else if(posElementDragged.left < posContainer.left && posContainer.left< posElementDragged.right) {
						tempArea = (posElementDragged.right - posContainer.left) * (posElementDragged.top - posContainer.bottom);
					}
				}
			}
			if (tempArea > overlapArea) {
				overlapArea = tempArea;
				container = $(this);
			}
		});
		return container;
	}
	
	var constr = function(el) {
		
		var draggableObjects = $(".draggable");
		
		var mouseUpHandler = function(event) {
			event.preventDefault();
			dropHandler(event);
			removeEvent(document.body, 'mouseup.tool_mm');
			removeEvent(document.body, 'mousemove.tool_mm');
		};
		
		var mouseMoveHandler = function(event){
			continueDragging(event);
		}
		
		/*
		* event Handler for the dragStartHandler event on the elements inside Tools
		*/
		var dragStartHandler = function(event, el, createClone) {
			elementDragged = el;
			elementDraggedOriginal = el;
			var width = $(elementDragged).width();
			var offset = $(elementDragged).offset();
			
			elementDraggesdPinPos = {
				left : (event.clientX - offset.left),
				top : (event.clientY - offset.top)
			};
			
			//Cloning and adding duplicate DOM element
			if(createClone) {
				var cloneObj = ($(elementDragged).clone())[0];
				$(elementDragged).addClass("dnd_disabled");
				elementDragged = cloneObj;
			}
			
			elementDragged.style.display="none";
			elementDragged.style.position ="absolute";
			
			
			$(elementDragged).width(width);
			$(elementDragged).appendTo($("body"));
		}

		attachEvent(el, 'mousedown.dnd',function (event) {
			if($(el).hasClass("dnd_dropped")) {
				dragStartHandler(event,el, false);
			} else {
				dragStartHandler(event,el, true);
			}
			attachEvent(document.body, 'mouseup.tool_mm',mouseUpHandler,false);
			attachEvent(document.body, 'mousemove.tool_mm',mouseMoveHandler,false);
		}, false);
		

		//for touch devices
		attachEvent(el, 'touchstart',function (event) {
			dragStartHandler(event.changedTouches[0], el, true);
			event.stopPropagation();
		}, false);
				
		attachEvent(el, 'touchmove',function (event) {
			event.preventDefault();
			continueDragging(event.changedTouches[0]);
		}, false);

		attachEvent(el, 'touchend',function (event) {
			event.preventDefault();
			dropHandler(event.changedTouches[0]);
		},false);
		
	}
	
	/********************************************************/
	/*                 Public   */
	/********************************************************/
	return	{
		"init":init
	}

})();
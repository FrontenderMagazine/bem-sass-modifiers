# Sassier (BE)Modifers

In early 2013, the “Block Element Modifier” (BEM) css syntax [emerged][1] as a popular way to create better organization and uniformity across projects. For the most part, I love it. It’s clear, organized, and it makes sense. But one thing that kept bugging me (and [my fellow FEDs][2]) was how redundant and long some of the class attributes on elements were getting—especially when it came to modifiers.

	<button class="button button--green button--rounded button--large">
	
## INDIVIDUAL MODIFIERS: A SHORTER SYNTAX

I really like the element--modifier notation visually. It makes it clear that the class is only meant to extend something. But repeating the element name each time is redundant. We could remove that redundancy *and* keep them visually distinct by attaching the modifiers directly to their element in our stylesheet, while keeping a leading hyphen on the class name to denote its “modifier”.

HTML

	<button class="button -green -rounded -large">
	
SCSS

	.button {
		&.-green {...}
		&.-rounded {...}
		&.-large {...}
	}
	
This maintains the clear distinction between elements and modifiers, without the need to repeat yourself—and yes, a single leading hyphen IS a valid character for the start of a selector ([double hyphens are not][3]). If multiple selectors make you uneasy, you’re probably having IE 6 flashbacks. Don’t worry. She can’t hurt you anymore.

Another concern may arise over the added specificity. I haven’t found it to be an issue, but can imagine a scenario where you had a set of global overrides you want to be able to add to any element. In that case, you could do something like this:

SCSS

	.button {
		&.-hoverable {
			&:hover {
				opacity: 0.75;
			}
		}
	}
 
	.overrides {
		&.-disabled {
			opacity: 0.25;
		}
	}
	
HTML

	<button class="button -hoverable overrides -disabled">Disabled</button>
	
The example is contrived, but you get the point. If you hover over the button the opacity does not change, because the .button.-hoverable class has been trumped the later defined and equally specific .overrides.-disabled selector.

## SAVED VARIATIONS: EXTENDING WITH SASS

The flexibility of using modifiers in our markup is great, but if I notice a commonly reoccurring combination, I prefer to combine them in my stylesheet instead. SASS @extend lets us do this.

SCSS

	.button--save {
		@extend %button;
		@extend %button--large;
		@extend %button--rounded;
		@extend %button--green;
	}
	
HTML

	<button class="button--save">Save</button>
	
So clean! You’ll notice a couple of things here. 1) I’m using the SASS % [placeholder][4] selector, and 2) I’m still using the normal BEM element--modifier syntax.

First, I create all of my styles using placeholder selectors, so I can @extend them into other classes later.

	%button {
		background: #45beff;
		border: none;
		padding: 1em 2em;
		font-size: 16px;
		
		&:hover {
		    opacity: 0.75;
		}
	}
 
	%button--green {
		background: #3efa95;
	}
	
	%button--red {
		background: #ff3a6a;
	}
 
	%button--large {
		font-size:20px;
	}
 
	%button--rounded {
		border-radius: 10px;
	}
	
Now I can assemble my element styles, expose any modifiers *I plan on using in the markup*, and create reusable variations that extend from various combinations of modifiers.

	.button {
		@extend %button;
 
		&.-green {
			@extend %button--green;
		}
  
		&.-large {
			@extend %button--large;
		}
	}
 
	.button--delete {
		@extend %button;
		@extend %button--large;
		@extend %button--rounded;
		@extend %button--red;
	}
	
[See it on CodePen.][5]

## BEM TO BEVM?

When all’s said and done, the BEM block__element--modifer pattern has morphed into something more like block__element--variation -modifier, plus internal SASS %modifier selectors.

	// Internal Modifier
	%button--red {
		background: #ff3a6a;
	}
 
	// Element
	.button {
		// Modifier
		&.-red {
			@extend %button--red;
		}
	}
 
	// Variation
	.button--delete {
		@extend %button;
		@extend %button--red;
	}
	
I’m enjoying it so far, but what do you think? Useful? Weird? Discuss!

[1]: http://csswizardry.com/2013/01/mindbemding-getting-your-head-round-bem-syntax/
[2]: http://viget.com/extend/bem-multiple-modifiers-and-experimenting-with-attribute-selectors
[3]: http://www.w3.org/TR/CSS2/syndata.html#characters
[4]: http://blog.teamtreehouse.com/extending-placeholder-selectors-with-sass
[5]: http://codepen.io/greypants/pen/hvrHb

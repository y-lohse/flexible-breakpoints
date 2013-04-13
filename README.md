# Flexible breakpoints

A couple of SASS mixins that make creating and maintaining breakpoints for responsive web designs 
easy as shit.

## SASS media queries

Nobody wants to write their media queries more than once. Seriously.

```
@media only screen and (min-width: 700px) and (max-width: 1200px)
```

That's logn, hard to read, hard to maintain. Sass makes it much easier with the @content directive.
[Here](http://thesassway.com/intermediate/responsive-web-design-in-sass-using-media-queries-in-sass-32)
and [there](http://css-tricks.com/media-queries-sass-3-2-and-codekit/) is how you set it up, and 
afterwards you can go 

```
#selector{
	//some common stuff
	property: value;
	property: value;
	property: value;
	
	//and the magic
	@include breakpoint(big-screen){
		//specific rules for big screens
		property: value;
	}
}
```

That's awesome.

## Using variables

Now usually, I maintain a couple of variables that determine my breakpoints, like so :

```
$media-desktop: 960px;
$media-tablet: 760px;
$media-phone: 340px;
```

That's mainly because I tend to use those values not only in media queries (but also in container width
for example).

It's not too hard to derive the SASS mixins linked above to work with variables... ***but**

I found it get's messy when you want to add a new breakpoint, or you want to target say 
anything **smaller** than a certain breakpoint. When that happens I have to rewrite my breakpoint 
mixin, and it sucks.

Also, the variable names is an issue. My variable may be called *$media-tablet*, but the call
might be *@include breakpoint(tablet)*. And when on the next project I want to rename the breakpoints
because I realize they were dumb, I have more rewriting to do.

## SO

So I wrote some mixins that keep what I like in this system, add what I feel is lacking and suppresses
the rewriting issues. It's 60 lines so if you want to know how it works behidn teh scene, just read the code.

Here's how you use it. There's also and usage.scss file with... well, usage examples.

## Usage

1. Define your breakpoints
2. Register 'em
3. Let the media queries rain

### Defining breakpoints

Name them how ever the fuck you want.

```
$heavy-weight: 1400px;
$middle-weight: 960px;
$welter-weight: 700px;
$feather-weight: 300px;
```

Good.

### Registering

There is one variable that will leak in the global scope, which is by default called "breakpoints".
The name is fairly generic so if you're affraid of collision just change it. It holds the list
of registered breakpoints, and this is necessary if you want the advanced (and awesome) parts of the
mixins to work.

```
@include register-breakpoints($heavy-weight, $middle-weight, $welter-weight, $feather-weight);
```

That's it. The breakpoint order doesn't matter (they get sorted afterwards).

### Adding media queries

There are 4 mixins you can now use.

#### width-matches

Probably the most usefull one.

```
@include width-matches($middle-weight){
	//do stuff
}
```

This will create a media query that matches everything raning from the $middle-weight 
size (960px) to $heavy-weight (1399px).

Except for the extremes. If you pass the biggest breakpoint as argument, it will match everything
equal or above. If you call the mixin with the smallest breapoint as argument, it will match
everything smaller than the second smallest breakpoint.

```
@include width-matches($heavy-weight){
	//equal or larger than 1400px
}
@include width-matches($feather-weight){
	//smaller than 700px (the breakpoint just above $feather-weight)
}
```

#### width-above

Matches everything equal or larger than the given width.

```
@include width-above($feather-weight){
	//everything larger than 300px
}
```

#### width-below

Matches everything smaller than the given width.

```
@include width-above($feather-weight){
	//everything smaller than 300px
}
```

#### width-between

Matches everything between the 2 widths

```
@include width-between($feather-weight, $middle-weight){
	//everything between 300 and 959px
}
```

## That's it

Now you can add another breakpoint 6 month later and it's not too much of a hassle.


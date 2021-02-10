# qmk_rgb_on_off_writeup
A brief writeup for enabling individual rgb cells on keypress for a qmk keyboard
with an rgb matrix.
_ _ _

Place this code into the keymap.c file for the keyboard you are using. You
should place it above the `process_record_user()` function. It will instantiate
the variables we need.

```c
int row;
int col;
int loc = 255;
```

Inside of the `process_record_user()` function (also in your keyboard's keymap.c
file, we will place the following code:

```c
row = record->event.key.row;
col = record->event.key.col;
loc = (row*12)+col;
//  necessary due to the single
//  2u spacebar of the mit
//  layout. Else, not needed
if ((loc > 41) && (loc != 255)) {
	loc = loc+1;
}
if (!(record->event.pressed)) {
    any_on = false;
    loc = 255;
    rgb_matrix_indicators_user();
}
```

Somewhere near the bottom of your keymap.c, there should be a
`rgb_matrix_indicators_user()` function. If this function does not exist in your
keymap, you can define it as such at the bottom of your keymap.c:

```c
void rgb_matrix_indicators_user(void) {
}
```

Then inside of the function (between the curly braces), you can place this code
(minus the function definition and final curly brace):

```c
void rgb_matrix_indicators_user(void) {
	int space[] = {41, 42, 43};
	if (loc < 41)  {
		rgb_matrix_set_color(loc, 150, 150, 150);
	}
	 if (loc == 41) {
		foreach(int *v, space) {
			 rgb_matrix_set_color(*v, 150, 150, 150);
		}
	}
	if ((loc > 41) && (loc != 255)) {
		rgb_matrix_set_color(loc, 150, 150, 150);
	}
	if (loc == 255) {
		return;
	}
}
```

I no longer have the keymap.c for my planck lite, or the planck lite itself, so
I am unable to test this code against the latest version QMK, but this should be
a good jumping off point.


# Pixie Function Reference

Brief listing of all Pixie functions, with basic examples. WIP.


## Basic data types

The built-in C/C++ types are uses as is:
- char
- int
- float
- double

For signed integers of specific size, the following are defined: 
- i8
- i16
- i32
- i64
    
And for unsigned integers: 
- u8
- u16
- u32
- u64


## Debug
    
### PIXIE_ASSERT

Displays a message if an expression is false.

    PIXIE_ASSERT( my_value < 10, "Value is too high" );


### PIXIE_ASSERTF
    
Displays a formatted message if an expression is false. Note the extra parenthesis required around message and its params.

    PIXIE_ASSERTF( my_value < 10, ( "Value is too high, was %d, can be max %d", my_value, 10 ) );

### stdprint

Prints to stdout. Works exactly like printf, but takes a pixie *string* as format string.

	string a = "Test string";
	stdprint( b );

	string b = "Answer: %d";
	stdprint( b, 42 );
	
### cpu_bar

The cpu bars are a very basic CPU profiling system. As Pixie runs on a fixed 60 fps, it means executing a frame must take no longer than 16.7 milliseconds (1/60). The *cpu_bar* function sets the background color of the screen to the specified color, on the vertical position proportional to *when* during a frame update it is called. You call *cpu_bar* with a color before you perform a chosen part of your frame update, and then call *cpu_bar* without parameters after it is done, and the result is a colored bar that takes up a section of the screen proportional to how big a percentage, out of 16.7 milliseconds, the code took to execute. It is a nice, simple, visual way to gauge the approximate and relative performance cost of parts of your code. It is not meant as a replacement for more advanced profiling techniques, such as using a code profiler.
If your entire frame update takes more than 16.7 milliseconds, you will not see any bars, but instead a constant flickering color (if using cpu bars).    

	cpu_bar( 0, 255, 0 ); // set color to green
	// do some work here, maybe draw something
	cpu_bar( 0, 0, 255 ); // set color to blue
	// do some other work
	cpu_bar(); // end color bar

### cpu_bars_enabled

Disabled or enables the cpu bars system. By disabling the cpu bars, it is safe to leave calls to *cpu_bar* sprinkled throughout the code - they won't do anything unless enabled.

	bool b = cpu_bars_enabled(); // query current state
	cpu_bars_enabled( true ); // turn on cpu bars
	cpu_bars_enabled( false ); // turn off cpu bars


## ref

Reference counted smart-pointer. Objects created this way are automatically destroyed when the last reference to it is released.

	struct my_class
		{
		my_class( int param1, int param2 );
		void method();
		};

	ref<my_class> my_obj = new_ref<my_class>( 1, 2 ); // create instance, refcount is 1
	my_obj->method(); // accessed like a normal pointer

	ref<my_class> copy = my_obj; // 'copy' refers to the same instancem refcount is 2

	my_class* ptr = my_obj; // can be implicitly converted to pointer
	// Note: if the my_obj reference count reaches 0, the above ptr will be invalid so use with care
 

## Strings

In general, string manipulation functions can be called on either *string* or *string_id* types, or with a string literal ("my string"). 


### String types

- string

  Case sensitive ascii string

- string_id

  Case insensitive ascii string to use for ID's.

### Construction

	string a = "test";
	string b( "test" );
	string c = a;
	string_id d = a;
	string_id e( a );
	char const* f = "test string";
	string g( f, f + 4 ); // from begin/end char pointers
	// g is "test"


### string.c_str / string_id.c_str

Converts from *string* or *string_id* to C string

	string a = "Pixie game library";
	char const* b = a.c_str();

      
### string.length / string_id.length

Returns the number of characters in a string

	string a = "Pixie game library";
	int b = a.length();
	// b is 18

      
### String operators

All string operators works on both *string* and *string_id* types, and will work with string literals as long as at least one of the operands is of *string* or *string_id* type.


#### Concatenation

    string a = "A";
    string b = "B";
    string c = a + b;
    string d = a + "B";
    string e = "A" + b;


#### Appending
    
    string a = "A";
    string b = "B";
    a += b; 
    a += "C"; 
	// a is now "ABC"
    
    
#### Comparison

	string a = "String A";
	string b = "String B";
	bool c = ( a == b );
	bool d = ( a != b ); 
	bool e = ( a > b );
	bool f = ( a < b );   
	bool g = ( a >= b );
	bool h = ( a <= b );   

    
### trim

Removes leading and trailing whitespace

	string a = "   Test    ";
	string b = trim( a );
	// b is "Test"


### ltrim

Removes leading whitespace

	string a = "   Test    ";
	string b = trim( a );
	// b is "Test    "


### rtrim

Removes trailing whitespace

	string a = "   Test    ";
	string b = trim( a );
	// b is "   Test"
 

### left

Return the leftmost characters of a string

	string a = "Pixie game library";
	string b = left( a, 5 );
	// b is "Pixie" 


### right

Return the rightmost characters of a string

	string a = "Pixie game library";
	string b = right( a, 7 );
	// b is "library" 


### mid

Return a number of characters from the middle of a string

	string a = "Pixie game library";
	string b = mid( a, 6, 4 );
	// b is "library" 
	string c = mid( a, 6 );
	// c is "game library"


### instr

Search for occurrences of one string within another string

	string a = "Pixie game library. To make games.";
	int b = instr( a, "game" );
	// b is 6
	int c = "game";
	int d = instr( a, c, b + 1 );
	// d is 28 
 

### any

Search for the next occurrence of any character of one string within another string

	string a = "Pixie game library";
	string b = "xy "; // search for x, y or space
	int c = any( a, b );
	// c is 2
	int d = any( a, b, c + 1 );
    // d is 5 
	int e = any( a, "xy", d + 1 ); // search for x and y only
    // e is 17

 
### upper

Convert a string to upper case

	string a = "Pixie Game Library";
	string b = upper( a );
	// b is "PIXIE GAME LIBRARY"


### lower

Convert a string to lower case

	string a = "Pixie Game Library";
	string b = lower( a );
	// b is "pixie game library"


### str

Convert a number into a string. See *format* for formatted string output.

	string a = str( 42 );
	// a is "42"
	string b = str( 3.14f );
	// b is "3.140000"


### val	

Convert a string of digits into a floating point value

	string a = "3.14";
	float b = val( a );
	// b is 3.14f


### integer	

Convert a string of digits into an integer value

	string a = "42";
	int b = integer( 42 );
	// b is 42


### space

Generate a string consisting of a number of space characters

	string a = space( 3 );
	// a is "   "
	
 
### flip

Reverse a string

	string a = "abc";
	string b = flip( a );
	// b is "cba";

 
### repeat

Generates a string by repeating another string a number of times

	string a = "abc";
	string b = repeat( a, 3 );
	// b is "abcabcabc"


### starts_with

Chheck if the start of a string matches another string

	string a = "Pixie game library";
	bool b = starts_with( a, "Pixie" );
	// b is true


### chr

Return the character with a given ASCII code

	string a = chr( 65 );
	// a is "A"


### asc

Give the ASCII code of the first character of a string

	string a = "ABC";
	int b = asc( a );
	// b is 65


### len 

Give the length of a string

	string a = "Pixie game library";
	int b = len( a );
	// b is 18


### format

Use *printf* style formatting to generate a string
	
	string a = format( "The answer is: %d. I am %.2f percent sure!", 42, 100.0f );
	// a is "The answer is: 42. I am 100.00 percent sure!"
 

### str_switch / str_case

Compare *string_id* strings in a way similar to switch statements. 

	string_id a = "Test 2";
	int x = 0; 
	str_switch( a )
		{
		str_case( "Test 1" )
			{
			x = 1;
			}
		str_case( "Test 2" )
			{
			x = 2;
			}
		str_case( "Test 3" )
			{
			x = 3;
			}
		}
	// x is 2

 

## Integer math


### abs

Absolute value.

	int x = -10;
	int y = abs( x );
	// y is 10


### min

Returns the smallest of two values.

	int x = min( 10, 20 );
	// x is 10


### max

Returns the largest of two values.

	int x = max( 10, 20 );
	// x is 20


### clamp

Clamp value to a range.

	int x = clamp( 10, 1, 5 );
	// x is 5
	int y = clamp( -10, 1, 5 );
	// y is 1


### pow2_ceil

Rounds an integer value up to the nearest highest power-of-two value.

	int x = pow2_ceil( 13 );
	// x is 16


### fast_round 

Returns the integer value that is closest to x, with halfway cases rounded away from zero. Uses SSE intrinsics if available, but with a reasonably fast fallback for when they are not.

	int x = fast_round( 1.5f );
	// x is 2
	int y = fast_round( -1.5f );
	// y is -2
	

## Floating-point math


### PI / TWO_PI

The ratio of a circle’s circumference to its diameter.

	float x = PI;
	// x is 3.14159274f
	float y = TWO_PI;
	// y is 6.28318548f


### to_radians

Converts an angle from radians to degrees.

	float x = to_radians( 90.0f );
	// x is 3.14159274f


### to_degrees

Converts an angle from degrees to radians.

	float x = to_degrees( PI );
	// x is 180.000000f


### angle_diff

Calculates the difference between two angles, handling wrap-around. Returns a value between -PI and PI. 

	float x = angle_diff( -2.5f * PI, 17.2f * PI );
	// x is -0.942477226f
	float y = angle_diff( 17.2f * PI, -2.5f * PI );
	// x is 0.942477226f


### angle_lerp

Linear interpolation of angles, handling wrap-around. 

	float x = angle_lerp( 0.0f, 5.0f * PI, 0.5f );
	// x is 1.57079625f
	float y = angle_lerp( 0.0f, 5.0f * PI, 0.0f );
	// y is 0.000000000f
	float z = angle_lerp( 0.0f, 5.0f * PI, 1.0f );
	// z is 3.14159250f


## Vector math


### Vector types

- float2
- float3
- float4


### Matrix types

- float2x2
- float2x3
- float3x2
- float3x3
- float2x4
- float3x4
- float4x2
- float4x3
- float4x4


### Construction

Examples for float3, works the same for all vector and matrix types 

	float3 a( 1.0f, 2.0f, 3.0f );
	float3 b( 1.0f );
	float values[] = { 0.0f, 1.0f, 2.0f, };
	float3 c( values );

To construct matrix types, you can also supply a vector type for each row

	float3 a( 1.0f, 2.0f, 3.0f );
	float3x3 b( a, a, a ); 


### Elements

Access vector elements by x/y/z/w members, or array index
	
	float3 a( 1.0f, 2.0f, 3.0f );
	float b = a.x;
	// b is 1.0f
	float c = a.y;
	// c is 2.0f
	float d = a[ 2 ];
	// d is 3.0f

For matrix types, access each row the same way, producing a vector type 
	
	float3x3 a( 1.0f, 2.0f, 3.0f, 4.0f, 5.0f, 6.0f, 7.0f, 8.0f, 9.0f );
	float3 b = a.x;
	// b is a float3 containing 1.0f, 2.0f, 3.0f
	float3 c = a.y;
	// c is a float3 containing 4.0f, 5.0f, 6.0f
	float3 d = a[ 2 ];
	// d is a float3 containing 7.0f, 8.0f, 9.0f

Combine both access methods to access individual elements of a matrix

	float3x3 a( 1.0f, 2.0f, 3.0f, 4.0f, 5.0f, 6.0f, 7.0f, 8.0f, 9.0f );
	float b = a.x.y;
	// b is 2.0f
	float c = a.y.x;
	// c is 4.0f
	float d = a[ 2 ][ 1 ];
	// d is 8.0f

Vector classes have r/g/b/a alias for x/y/z/w respectively (as in red/green/blue/alpha). These are methods, so requires parenthesis at the end. 

	float3 a( 1.0f, 2.0f, 3.0f );
	float b = a.r();
	// b is 1.0f
	float c = a.g();
	// c is 2.0f
	float d = a.b();
	// d is 3.0f

 
### Swizzling

Just like in many GPU shader languages, you can swizzle vector elements, creating a new vector out of any combination of elements from an existing vector. 
Note that these are method calls, not properties like they are in shader languages.

	float3 v0( 1.0f, 2.0f, 3.0f );
	float3 v1 = v0.zxy();
	float2 v2 = v0.xz();
	float4 v3 = v0.xxyy();
	float3 v4 = v1.bgr();


### Operators

The vector types have the usual overloads for addition, subtraction, multiplication, division and equality/inequality.

	float3 a( 1.0f, 2.0f, 3.0f );
	float3 b = -a; 
	// b is ( -1.0f, -2.0f, -3.0f )

	bool c = ( v == v );
	// c is true

	bool d = ( v != v );
	// d is false

	// The following is supported for +=  -=  *=   /=

	float3 e( 1.0f, 2.0f, 3.0f );
	e += 0.5f;
	// e is ( 1.5f, 2.5f, 3.5f );
	
	float3 f( 1.0f, 2.0f, 3.0f );
	f += float3( 1.5f, 2.5f, 3.5f;
	// f is ( 2.5f, 4.5f, 6.5f );

	// The following is supported for +  -  *   /

	float3 g( 1.0f, 2.0f, 3.0f );
	float3 h( 2.0f, 4.0f, 8.0f );
	float3 i = g + h;
	// i  is ( 3.0f, 6.0f, 11.0f );
	 
	float3 j( 1.0f, 2.0f, 3.0f );
	float3 k = j + 2.0f;
	// k  is ( 3.0f, 4.0f, 5.0f );


### abs

Absolute value (per component).

	float3 x = abs( float3( -1.1f, 0.0f, 1.1f ) );

### acos
	
The arccosine of each component.

	float3 x = acos( float3( -1.1f, 0.0f, 1.1f ) );

### all

Test if all components are non-zero.

	bool x = all( float3( -1.1f, 0.0f, 1.1f ) );

### any

Test if any component is non-zero.

	bool x = any( float3( -1.1f, 0.0f, 1.1f ) );

### asin

The arcsine of each component.

	float3 x = asin( float3( -1.1f, 0.0f, 1.1f ) );

### atan

The arctangent of each component.

	float3 x = atan( float3( -1.1f, 0.0f, 1.1f ) );

### atan2

The arctangent of of two values (x,y), per component.	

	float3 x = atan2( float3( 2.0f ), float3( -1.1f, 0.0f, 1.1f ) );

### ceil

The smallest integer which is greater than or equal to the value, per component.

	float3 x = ceil( float3( -1.1f, 0.0f, 1.1f ), float3( -1.0f ), float3( 1.0f ) );

### clamp

Clamps to the range [min, max], per component.

	float3 x = ceil( float3( -1.1f, 0.0f, 1.1f ) );

### cos

The cosine of each component.

	float3 x = cos( float3( -1.1f, 0.0f, 1.1f ) );

### cosh
 
The hyperbolic cosine of each component.

	float3 x = cosh( float3( -1.1f, 0.0f, 1.1f ) );

### cross

Returns the cross product of two 3D vectors. Only supported for float3.

	float3 x = cross( float3( -1.1f, 0.0f, 1.1f ), float3( 1.0f, 2.0f, 3.0f ) );

### degrees

Converts from radians to degrees, per component.

	float3 x = degrees( float3( -1.1f, 0.0f, 1.1f ) );

### distance
	
Returns the distance between two points. Not supported for matrix types.

	float3 x = distance( float3( -1.1f, 0.0f, 1.1f ), float3( 1.0f, 2.0f, 3.0f ) );

### distancesq

Returns the squared distance between two points. Not supported for matrix types.

	float3 x = distancesq( float3( -1.1f, 0.0f, 1.1f ), float3( 1.0f, 2.0f, 3.0f ) );

### dot

Returns the dot product of two vectors. Not supported for matrix types.

	float x = dot( float3( -1.1f, 0.0f, 1.1f ), float3( 1.0f, 2.0f, 3.0f ) );

### exp 

The base-e exponent, per component.

	float3 x = exp( float3( -1.1f, 0.0f, 1.1f ) );

### exp2

The base 2 exponent, per component.

	float3 x = exp2( float3( -1.1f, 0.0f, 1.1f ) );

### floor

The greatest integer which is less than or equal to the value, per component.

	float3 x = floor( float3( -1.1f, 0.0f, 1.1f ) );

### fmod

Returns the floating point remainder of a/b, per component.

	float3 x = fmod( float3( -1.1f, 0.0f, 1.1f ), float3( 1.0f, 2.0f, 3.0f ) );

### frac

The fractional part of x, per_component.

	float3 x = frac( float3( -1.1f, 0.0f, 1.1f ) );

### length

Returns the length of the vector. Not supported for matrix types.

	float3 x = length( float3( -1.1f, 0.0f, 1.1f ) );

### lengthsq

Returns the squared length of the vector. Not supported for matrix types.

	float3 x = lengthsq( float3( -1.1f, 0.0f, 1.1f ) );

### lerp

Linear interpolation, per component.

	float3 x = lerp( float3( -1.1f, 0.0f, 1.1f ), float3( 1.0f, 2.0f, 3.0f ), float3( 0.5f ) );

### log

The base-e logarithm, per component.

	float3 x = log( float3( -1.1f, 0.0f, 1.1f ) );

### log2

The base-2 logarithm, per component.

	float3 x = log2( float3( -1.1f, 0.0f, 1.1f ) );

### log10

The base-10 logarithm, per component.

	float3 x = log10( float3( -1.1f, 0.0f, 1.1f ) );

### mad

Performs an arithmetic multiply/add operation on three values. Per component.

	float3 x = mad( float3( -1.1f, 0.0f, 1.1f ), float3( 1.0f, 2.0f, 3.0f ), float3( 4.0f ) );

### max

Returns the greater of two values, per component.

	float3 x = max( float3( -1.1f, 0.0f, 1.1f ), float3( 1.0f ) );

### min

Returns the lesser of two values, per component.

	float3 x = min( float3( -1.1f, 0.0f, 1.1f ), float3( 1.0f ) );

### mul

Performs matrix multiplication between two types. Supported for a range of vector/matrix type combinations.
mul can be called for all matrix and vector types with a single float as either the first or second argument, and will then behave the same as with the * operator.
Additionally, the following combinations are also supported:
	
	float2 mul( float2, float2x2 )
	float3 mul( float2, float2x3 )
	float2 mul( float3, float3x2 )
	float3 mul( float3, float3x3 )
	float4 mul( float2, float2x4 )
	float4 mul( float3, float3x4 )
	float2 mul( float4, float4x2 )
	float3 mul( float4, float4x3 )
	float4 mul( float4, float4x4 )	
	
	float2 mul( float2x2, float2 )
	float2 mul( float2x3, float3 )
	float3 mul( float3x2, float2 )
	float3 mul( float3x3, float3 )
	float2 mul( float2x4, float4 )
	float3 mul( float3x4, float4 )
	float4 mul( float4x2, float2 )
	float4 mul( float4x3, float3 )
	float4 mul( float4x4, float4 )
	
	float2x2 mul( float2x2, float2x2 )
	float3x3 mul( float3x2, float2x3 )
	float2x2 mul( float2x3, float3x2 )
	float3x3 mul( float3x3, float3x3 )
	float4x4 mul( float4x2, float2x4 )
	float4x4 mul( float4x3, float3x4 )
	float2x2 mul( float2x4, float4x2 )
	float3x3 mul( float3x4, float4x3 )
	float4x4 mul( float4x4, float4x4 )

mul can also be used on two vectors of the same type. This is the same as calling the dot function, and returns the dot product.

### normalize

Returns a normalized vector. Not supported for matrix types.

	float3 x = normalize( float3( -1.1f, 0.0f, 1.1f ) );

### pow

Raise a value to the specified power, per component.

	float3 x = pow( float3( -1.1f, 0.0f, 1.1f ), float3( 1.0f, 2.0f, 3.0f ) );

### radians 

Converts from degrees to radians, per component.

	float3 x = radians( float3( -1.1f, 0.0f, 1.1f ) );

### rcp
	
Calculates a per-component reciprocal.

	float3 x = rcp( float3( -1.1f, 0.0f, 1.1f ) );

### reflect

Calculates a reflection vector using an incident ray and a surface normal. Not supported for matrix types.

	float3 n = float3( 1.0f, 2.0f, 3.0f )
	float3 v = float3( -1.1f, 0.0f, 1.1f )
	float3 x = reflect( v, n );

### refract

Calculates a refraction vector using an entering ray, a surface normal, and a refraction index. Not supported for matrix types.

	float3 n = float3( 1.0f, 2.0f, 3.0f )
	float3 v = float3( -1.1f, 0.0f, 1.1f )
	float3 x = reflect( v, n, 0.5f );

### round

Rounds to the nearest integer, per component.

	float3 x = round( float3( -1.1f, 0.0f, 1.1f ) );

### rsqrt

Calculates a per component reciprocal square root.

	float3 x = rsqrt( float3( -1.1f, 0.0f, 1.1f ) );

### saturate

Clamps to the range [0, 1], per component.

	float3 x = saturate( float3( -1.1f, 0.0f, 1.1f ) );

### sign

Returns -1 if the value is less than zero, 0 if it equals zero, and 1 if it is greater than zero. Per component.

	float3 x = sign( float3( -1.1f, 0.0f, 1.1f ) );

### sin

The sine of each component

	float3 x = sin( float3( -1.1f, 0.0f, 1.1f ) );

### sinh

The hyperbolic sine of each component.

	float3 x = sinh( float3( -1.1f, 0.0f, 1.1f ) );

### smoothstep

Calculates a smooth Hermite interpolation between 0 and 1, per component. Returns 0 if x is less than min; 1 if x is greater than max; otherwise, a value between 0 and 1 if x is in the range [min, max].

	float3 x = smoothstep( float3( -2.0f ), float3( 2.0f ), float3( -1.1f, 0.0f, 1.1f ) );

### smootherstep

Alternative smoothstep which has zero 1st- and 2nd-order derivatives at 0 and 1.
	
	float3 x = smootherstep( float3( -2.0f ), float3( 2.0f ), float3( -1.1f, 0.0f, 1.1f ) );

### sqrt

The square root of each component.

	float3 x = sqrt( float3( 1.0f, 2.0f, 3.0f ) );

### step

Compares two values, returning 0 or 1 based on which value is greater. Per component.

	float3 x = step( float3( -1.1f, 0.0f, 1.1f ), float3( 1.0f, 2.0f, 3.0f ) );

### tan

The tangent of each component.

	float3 x = tan( float3( -1.1f, 0.0f, 1.1f ) );

### tanh

The hyperbolic tangent of each component.

	float3 x = tanh( float3( -1.1f, 0.0f, 1.1f ) );

### trunc

Truncates each component to integer value.

	float3 x = trunc( float3( -1.1f, 0.0f, 1.1f ) );

### transpose

Returns the transpose of a matrix.

	float2x3 x = transpose( float3x2( 0.0f, 1.0f, 2.0f, 3.0f, 4.0f, 5.0f ) );

### determinant
	
Returns the determinant of a square matrix

	float x = determinant( float2x2( 0.0f, 1.0f, 2.0f, 3.0f ) );

### is_identity

Returns true if a square matrix is an identity matrix.

	bool x = is_identity( float2x2( 1.0f, 0.0f, 0.0f, 1.0f ) );

### inverse

Calculates the inverse of a matrix. If matrix inversion fails, false is returned. Gives the inverted matrix and the determinant in the first and second parameter, respectively. 

	float2x2 inverted_matrix;
	float det;
	bool x = inverse( &inverted_matrix, &det, float2x2( 1.0f, 0.0f, 0.0f, 1.0f ) ); 


## Entry point and frame update

### run

Starts a Pixie instance. Will call the supplied function, after initializing Pixie for the calling thread. Note that Pixie functions can be called only from the thread running the supplied function. It is possible to call *run* from multiple threads, to have several simultaneous and independant Pixie instances, each with their own window and execution contexts (useful for making editors, for example). 
	
	int my_main_game_func()
		{
		// call pixie functions from here, implementing your game.
		return 0;
		}

	int main()
		{
		return run( my_main_game_func );
		}

	
### run_async
	
Same as *run*, but starts the pixie instance on a different thread, and returns immediately. It returns a handle which can be used in the function *join_async* to wait for the pixie instance to finish.   

	int my_main_game_func()
		{
		// call pixie functions from here, implementing your game.
		return 0;
		}

	int main()
		{
		async_handle handle = run_async( my_main_game_func );
		return join_async( handle );
		}

### join_async

Waits for a pixie instance (started by calling *run_async*) to finish.
	
### execute_frame

With Pixie, you are expected to implement your own main game loop (or loops). Once per iteration, you need to call *execute_frame* to give Pixie a chance to read input, update sounds and display the current screen image.

	int my_main_game_func()
		{
		while( is_running() )
			{
			execute_frame();
			}
		return 0;
		}

### is_running

Returns true if the game is still running. If *end* have been called, *is_running* returns false.

###	exit_requested

Returns true if the user have clicked the *close* button on the window, or used the keyboard shortcut (Alt+F4) to close it. 

	while( is_running() )
		{
		execute_frame();		
		if( exit_requested() ) end();
		}


### cancel_exit_request

When *exit_requested* returns true, it can be appropriate to display a message asking if the player wants to exit, and if they say "yes", call the *end* function. If they say no, you can call *cancel_exit_request* to reset the *exit_requested* flag.

	while( is_running() )
		{
		execute_frame();		
		if( exit_requested() ) cancel_exit_request();
		}
	
### end

Tells the game to exit, with the specified return value (optional). Once called, *is_running* will return false, and the main loop is expected to exit. 

	while( is_running() )
		{
		execute_frame();		
		if( exit_requested() ) end();
		}

### system

Whereas the *end* function only sets the *is_running* flag to false, this *system* function will immediately force terminate all running instances of Pixie and return to the operating system.

	while( is_running() )
		{
		execute_frame();		
		if( exit_requested() ) system();
		}
	// Execution will never reach here


### wait

Waits for the specified period of time, given in number of jiffys (there are 60 jiffys in a second, and one frame of execution counts as 1 jiffy) if an integer is passed, or a number of seconds if a floating point value is passed.
The *wait* functions will automatically call *execute_frame* at appropriate intervals, meaning any ongoing *tween* actions will keep going, and sound/input is updated as normal.

	wait( 60 ); // Wait a second
	wait( 1.5f ); // Wait a second and a half

### wait_key

Waits for any key to be pressed.
*wait_key* will automatically call *execute_frame* at appropriate intervals, meaning any ongoing *tween* actions will keep going, and sound/input is updated as normal.

	wait_key();


## Events

Pixie uses simple events as an alternative to polling for input, or on a time delay. To receive an event, you implement a class inheriting the *event_handler* interface, and overriding the *on_event* method. Events are most useful in conjunction with the *game state* system.     

	struct my_event_handler 
		{ 
		virtual void on_event( string_id const& event, void* user_data ) override
			{
			str_switch( event )
				{
				str_case( "Exit" )
					{
					end();
					}
				}
			}
		};

###	remove_event_handler

Should be called from the destructor of any event handler instance, to ensure Pixie does not hold on to a reference to something which has been destroyed. Game states automatically call this in their destructor.

	struct my_event_handler 
		{ 
		virtual void on_event( string_id const& event, void* user_data ) override
			{
			// ...
			}
		
		~my_event_handler()
			{
			remove_event_handler( this );
			}
		};
	

### time_event		

Sets up an event handler to be called after a specified number of seconds. 
	
	my_event_handler handler;
	time_event( &handler, 5.0f, "Exit" ); // Trigger the "Exit" event in 5 seconds
	
### repeating_time_event

Sets up an event handler to be called periodically, with the specified interval in seconds. 
	
	my_event_handler handler;
	repeating_time_event( &handler, 1.0f, "Tick" ); // Trigger the "Tick" event every second


## Gamepad input
	
Pixie has a set of functions to read input from standard gamepads. It supports up to four gamepads, indexed 0 to 3.

### gamepad_connected

Check if a gamepad is connected 
	
	bool b = gamepad_connected( 0 );

### gamepad_axis

Read the current value of an axis of the gamepad. The supported axes are:

- GAMEPADAXIS_LEFT_TRIGGER
- GAMEPADAXIS_RIGHT_TRIGGER
- GAMEPADAXIS_LEFTSTICK_X
- GAMEPADAXIS_LEFTSTICK_Y
- GAMEPADAXIS_RIGHTSTICK_X
- GAMEPADAXIS_RIGHTSTICK_Y 

The function returns a floating point value in the range [-1, 1], except for the triggers which returns a value in the range [0, 1] 

	float x = gamepad_axis( 0, GAMEPADAXIS_LEFTSTICK_X );
	float y = gamepad_axis( 0, GAMEPADAXIS_LEFTSTICK_Y );

### Gamepad buttons		

The following gamepad buttons are supported:
- GAMEPADBUTTON_DPAD_UP
- GAMEPADBUTTON_DPAD_DOWN
- GAMEPADBUTTON_DPAD_LEFT
- GAMEPADBUTTON_DPAD_RIGHT
- GAMEPADBUTTON_START
- GAMEPADBUTTON_BACK
- GAMEPADBUTTON_STICK_LEFT
- GAMEPADBUTTON_STICK_RIGHT
- GAMEPADBUTTON_SHOULDER_LEFT
- GAMEPADBUTTON_SHOULDER_RIGHT
- GAMEPADBUTTON_A
- GAMEPADBUTTON_B
- GAMEPADBUTTON_X
- GAMEPADBUTTON_Y 

### gamepad_is_down

Check if a gamepad button is currently held down
	
	bool b = gamepad_is_down( 0, GAMEPADBUTTON_DPAD_DOWN );

### gamepad_was_pressed

Check if a gamepad button was pressed since the last call to *execute_frame*

	bool b = gamepad_was_pressed( 0, GAMEPADBUTTON_DPAD_DOWN );

### gamepad_was_released

Check if a gamepad button was released since the last call to *execute_frame*

	bool b = gamepad_was_released( 0, GAMEPADBUTTON_DPAD_DOWN );
	
### gamepad_pressed_event

Registers an event handler to be called when the specified gamepad button is pressed. See *Events* for how to set up an event handler.
	
	gamepad_pressed_event( &my_handler, 0, GAMEPADBUTTON_START, "StartEvent" );

### gamepad_released_event

Registers an event handler to be called when the specified gamepad button is released. See *Events* for how to set up an event handler.

	gamepad_released_event( &my_handler, 0, GAMEPADBUTTON_START, "StartEvent" );

### gamepad_axis_event

Registers an event handler to be called when the specified gamepad axis pass a specified threshold. See *Events* for how to set up an event handler.

	gamepad_axis_event( &my_handler, 0, GAMEPADAXIS_LEFTSTICK_X, -0.1f, "MoveLeft" );
	gamepad_axis_event( &my_handler, 0, GAMEPADAXIS_LEFTSTICK_X, 0.1f, "MoveRight" );


## Keyboard input

The following keyboard keys are recognized (there is also a constant, *KEYCOUNT*, which holds the number of KEY_.. values defined) 

- KEY_INVALID
- KEY_LBUTTON
- KEY_RBUTTON
- KEY_CANCEL
- KEY_MBUTTON
- KEY_XBUTTON1
- KEY_XBUTTON2
- KEY_BACK
- KEY_TAB
- KEY_CLEAR
- KEY_RETURN
- KEY_SHIFT
- KEY_CONTROL
- KEY_MENU
- KEY_PAUSE
- KEY_CAPITAL
- KEY_KANA / KEY_HANGUL 
- KEY_JUNJA 
- KEY_FINAL 
- KEY_HANJA
- KEY_KANJI / KEY_HANJA
- KEY_ESCAPE
- KEY_CONVERT
- KEY_NONCONVERT
- KEY_ACCEPT
- KEY_MODECHANGE
- KEY_SPACE
- KEY_PRIOR
- KEY_NEXT
- KEY_END
- KEY_HOME
- KEY_LEFT
- KEY_UP
- KEY_RIGHT
- KEY_DOWN
- KEY_SELECT
- KEY_PRINT
- KEY_EXEC
- KEY_SNAPSHOT
- KEY_INSERT
- KEY_DELETE
- KEY_HELP
- KEY_0
- KEY_1
- KEY_2
- KEY_3
- KEY_4
- KEY_5
- KEY_6
- KEY_7
- KEY_8
- KEY_9
- KEY_A
- KEY_B
- KEY_C
- KEY_D
- KEY_E
- KEY_F
- KEY_G
- KEY_H
- KEY_I
- KEY_J
- KEY_K
- KEY_L
- KEY_M
- KEY_N
- KEY_O
- KEY_P
- KEY_Q
- KEY_R
- KEY_S
- KEY_T
- KEY_U
- KEY_V
- KEY_W
- KEY_X
- KEY_Y
- KEY_Z
- KEY_LWIN
- KEY_RWIN
- KEY_APPS
- KEY_SLEEP
- KEY_NUMPAD0
- KEY_NUMPAD1
- KEY_NUMPAD2
- KEY_NUMPAD3
- KEY_NUMPAD4
- KEY_NUMPAD5
- KEY_NUMPAD6
- KEY_NUMPAD7
- KEY_NUMPAD8
- KEY_NUMPAD9
- KEY_MULTIPLY
- KEY_ADD
- KEY_SEPARATOR
- KEY_SUBTRACT
- KEY_DECIMAL, 
- KEY_DIVIDE
- KEY_F1
- KEY_F2
- KEY_F3
- KEY_F4
- KEY_F5
- KEY_F6
- KEY_F7
- KEY_F8
- KEY_F9
- KEY_F10
- KEY_F11
- KEY_F12
- KEY_F13
- KEY_F14
- KEY_F15
- KEY_F16
- KEY_F17
- KEY_F18
- KEY_F19
- KEY_F20
- KEY_F21
- KEY_F22
- KEY_F23
- KEY_F24 
- KEY_NUMLOCK
- KEY_SCROLL
- KEY_LSHIFT
- KEY_RSHIFT
- KEY_LCONTROL
- KEY_RCONTROL
- KEY_LMENU
- KEY_RMENU
- KEY_BROWSER_BACK
- KEY_BROWSER_FORWARD
- KEY_BROWSER_REFRESH
- KEY_BROWSER_STOP
- KEY_BROWSER_SEARCH
- KEY_BROWSER_FAVORITES
- KEY_BROWSER_HOME
- KEY_VOLUME_MUTE
- KEY_VOLUME_DOWN
- KEY_VOLUME_UP
- KEY_MEDIA_NEXT_TRACK
- KEY_MEDIA_PREV_TRACK
- KEY_MEDIA_STOP
- KEY_MEDIA_PLAY_PAUSE
- KEY_LAUNCH_MAIL
- KEY_LAUNCH_MEDIA_SELECT
- KEY_LAUNCH_APP1
- KEY_LAUNCH_APP2 
- KEY_OEM_1
- KEY_OEM_PLUS
- KEY_OEM_COMMA
- KEY_OEM_MINUS
- KEY_OEM_PERIOD
- KEY_OEM_2
- KEY_OEM_3
- KEY_OEM_4
- KEY_OEM_5
- KEY_OEM_6
- KEY_OEM_7
- KEY_OEM_8
- KEY_OEM_102
- KEY_PROCESSKEY
- KEY_ATTN
- KEY_CRSEL
- KEY_EXSEL
- KEY_EREOF
- KEY_PLAY
- KEY_ZOOM
- KEY_NONAME
- KEY_PA1
- KEY_OEM_CLEAR
- KEY_PEN_TOUCH
- KEY_PEN_LOWER_BUTTON
- KEY_PEN_UPPER_BUTTON

	
### key_is_down	

Check if a keyboard key is currently held down
	
	bool b = key_is_down( KEY_SPACE );

### key_was_pressed

Check if a keyboard key was pressed since the last call to *execute_frame*

	bool b = key_was_pressed( KEY_SPACE );

### key_was_released

Check if a keyboard key was released since the last call to *execute_frame*

	bool b = key_was_released( KEY_SPACE );

### key_pressed_event

Registers an event handler to be called when the specified keyboard key is pressed. See *Events* for how to set up an event handler.

	key_pressed_event( &my_handler, KEY_SPACE, "SpacePressedEvent" );


### key_released_event

Registers an event handler to be called when the specified keyboard key is released. See *Events* for how to set up an event handler.

	key_released_event( &my_handler, KEY_SPACE, "SpacePressedEvent" );

### ascii_event

Registers an event handler to be called whenever a specific character is entered on the keyboard. Characters are different than key codes, as some characters might require multiple key presses in a sequence. See *Events* for how to set up an event handler.
Note: This function is likely to change in a future release, as better support for reading ascii input is added.

	ascii_event( &my_handler, 'A', "EnteredA" );
	

## Action mapping

For games, it is often practical to be able to map multiple forms of input to the same abstract "action". Pixie has a system for setting up input mappings, and either poll them or receive events for them. 
The action mapping system supports the same keyboard keys and gamepad buttons/axes as the keyboard and gamepad function, and additionally supports the following mouse axes (mouse buttons are part of the keyboard keys):   

- MOUSEAXIS_LEFTRIGHT
- MOUSEAXIS_FORWARDBACK
- MOUSEAXIS_WHEEL

### action_axis

Creates an action mapping for an *axis*, which could be an actual mouse or gamepad axis, or it could be two gamepad buttons or keyboard keys used to emulate a positive/negative axis.
Returns a handle which can be used with *action_unmap* to remove the mapping.

	int handle1 = action_axis( "MoveUpDown", KEY_UP, KEY_DOWN );
	int handle2 = action_axis( "MoveUpDown", MOUSEAXIS_FORWARDBACK );
	int handle3 = action_axis( "MoveUpDown", 0, GAMEPADAXIS_LEFTSTICK_Y );
	int handle4 = action_axis( "MoveUpDown", 0, GAMEPADBUTTON_DPAD_UP, GAMEPADBUTTON_DPAD_DOWN );
				 
### action_button

Creates an action mapping for a *button*, which could be an actual  gamepad button or keyboard key, or it could a mouse or gamepad axis, plus a threshold value, emulating a button.
Returns a handle which can be used with *action_unmap* to remove the mapping.

	bool invert_axis = false;
	int handle1 = action_button( "Jump", KEY_SPACE );
	int handle2 = action_button( "Jump", MOUSEAXIS_WHEEL, invert_axis, 0.1f );
	int handle3 = action_button( "Jump", 0, GAMEPADAXIS_LEFT_TRIGGER, invert_axis, 0.1f );
	int handle4 = action_button( "Jump", 0, GAMEPADBUTTON_A );
	
### action_unmap

Removes a mapping created by *action_axis* or *action_button*

	int handle = action_button( "Pause", KEY_ESCAPE );
	action_unmap( handle );

### action_remove
	
Removes all mappings for the specified action.
	
	action_remove( "Jump" );
	
### action

Reads the current value of an action, checking all mapped input.
 
	float x = action( "Jump" );

### action_event

Registers an event handler to be called when the specified action is activated (for button actions) or goes beyond a specified threshold (for axis actions). See *Events* for how to set up an event handler.

	action_event( &my_handler, "Jump", "JumpEvent" );
	action_event( &my_handler, "MoveUpDown", 0.1f, "UpDownEvent" );

	
## Graphics tablet input

Pixie provides basic support for wacom graphics tablets.
	
### pen_x	

Returns the current x position of the pen on the graphics tablet, in screen coordinates.

	int pen_x();

### pen_y

Returns the current y position of the pen on the graphics tablet, in screen coordinates.

	int pen_y();

### pen_pressure

	float pen_pressure();

Returns the pressure of the pen tip on the graphics tablet, as a normalize value in [0, 1] range


## Mouse

There are functions to get and set the mouse pointer position, and to control its appearance.

### mouse_x

Returns the x coordinate of the mouse pointer, in screen coordinates.

	int x = mouse_x();

### mouse_y

Returns the y coordinate of the mouse pointer, in screen coordinates.

	int y = mouse_y();

### mouse_position

Sets the x and y coordinates of the mouse pointer, in screen coordinates.

	mouse_position( 100, 100 );

### limit_mouse

Restricts the mouse pointer to the specified area, in screen coordinates. Called without parameters to turn mouse limits off.

	limit_mouse( 50, 30, 270, 170 ); // limit mouse
	limit_mouse(); // turn off limit

### mouse_pointer	

Change the appearance of the mouse pointer. This can be either from a *bitmap*, or from a separate array for mask and pixels. Both options will use the currently set palette, but once set, the mouse pointer will not change if the palette changes (unless *mouse_pointer* is called again)

	ref<bitmap> bmp = load_bitmap( "my_pointer.png" );
	mouse_pointer( bmp, 0, 0 ); // hotspot at 0, 0

### auto_scale_mouse_pointer

Sets the mouse pointer to automatically scale with the pixel resolution of the windows, so that its pixels matches the screen pixels in size. 

	auto_scale_mouse_pointer( true );
	bool b = auto_scale_mouse_pointer();
	

## Game states

The Pixie game state system is useful to split your game into multiple states, such as title screen, intro, menu, options etc. It is not necessary to use game states, but it can make some things easier.
A game state have to be registered by calling *register_state* before you switch to it.

To define a game state, you need to implement the *game_state* interface, and (optionally) override the *tick* and *on_event* methods. 
	
	struct my_state : game_state
		{
		my_state()
			{
			// Initialize your state
			}
	
		virtual void tick() override
			{
			// Do per-frame updates here
			}

		virtual void on_event( string_id const& event, void* user_data ) override
			{
			// Receive events (game_state implements the *event_handler* interface)
			}
		};
	
The *tick* method will be called once for every call to *execute_frame* from your mainloop. 
The *on_event* method will be called for any event which has been set up with the state as event handler.


### game_state.time_event

Sets up the game state as an event handler to be called after a specified number of seconds. 
	
	struct my_state : game_state
		{
		my_state()
			{
			time_event( 2.0f, "TimeEvent" );
			}
		
		virtual void on_event( string_id const& event, void* user_data ) override
			{
			str_switch( event )
				{
				str_case( "TimeEvent" )
					{
					stdprint( "Time event triggered" );
					}
				}
			}
		};

### game_state.repeating_time_event

Sets up the game state as an event handler to be called periodically, with the specified interval in seconds. 

	struct my_state : game_state
		{
		my_state()
			{
			repeating_time_event( 2.0f, "RepeatingTimeEvent" );
			}
		
		virtual void on_event( string_id const& event, void* user_data ) override
			{
			str_switch( event )
				{
				str_case( "RepeatingTimeEvent" )
					{
					stdprint( "Repeating time event triggered" );
					}
				}
			}
		};


### game_state.gamepad_pressed_event
		void gamepad_pressed_event( int pad_index, gamepadbutton_id button, string_id const& event_id, void* user_data = 0 );

### game_state.gamepad_released_event
		void gamepad_released_event( int pad_index, gamepadbutton_id button, string_id const& event_id, void* user_data = 0 );

### game_state.gamepad_axis_event
		void gamepad_axis_event( int pad_index, gamepadaxis_id axis, float threshold, string_id const& event_id, void* user_data = 0 );

### game_state.key_pressed_event
		void key_pressed_event( key_id key, string_id const& event_id, void* user_data = 0 );

### game_state.key_released_event
		void key_released_event( key_id key, string_id const& event_id, void* user_data = 0 );

### game_state.ascii_event
		void ascii_event( char ascii, string_id const& event_id, void* user_data = 0 );

### game_state.action_event
		void action_event( string_id const& action, string_id const& event_id, void* user_data = 0 );
		void action_event( string_id const& action, float threshold, string_id const& event_id, void* user_data = 0 );

### register_state
	template< typename T > void register_state();

### switch_state
	template< typename T > void switch_state( float trans_out = 0.0f, float trans_in = 0.0f );

## Systems

### add_system	
	template< typename T > void add_system( T* system );

### remove_system
	template< typename T > void remove_system();

### system
	template< typename T > T* system();


## Palette and colors

### color_resolution_t
	enum color_resolution_t
		{
		COLOR_RESOLUTION_GRAY1, COLOR_RESOLUTION_GRAY2, COLOR_RESOLUTION_GRAY4, COLOR_RESOLUTION_GRAY8, 
		COLOR_RESOLUTION_RGB3, COLOR_RESOLUTION_RGB6, COLOR_RESOLUTION_RGB8, COLOR_RESOLUTION_RGB9, COLOR_RESOLUTION_RGB12,
		COLOR_RESOLUTION_RGB15, COLOR_RESOLUTION_RGB16, COLOR_RESOLUTION_RGB18, COLOR_RESOLUTION_RGB24,
		};
	
### color_resolution
	void color_resolution( color_resolution_t res );
	color_resolution_t color_resolution();
	
### rgb
	struct rgb final
		{
		u8 r, g, b; 
		
		rgb();
		rgb( int r_, int g_, int b_ );
		
		rgb( u32 xbgr );
		operator u32();
		
		rgb( float3 rgb );
		operator float3();
	
		rgb( float4 rgb );
		operator float4();
		};

### load_palette	
	void load_palette( string const& filename );
	void load_palette( string const& filename, rgb colors[ 256 ] );

### palette
	rgb const* palette();
	void palette( rgb const* colors, int count, int start = 0 );
	rgb* palette( int y_pos );
	void palette( int y_pos, rgb const* colors, int count, int start = 0 );

### palette_color
	rgb palette_color( int index);
	void palette_color( int index, rgb color ); 
	void palette_color( int index, u8 r, u8 g, u8 b ); 
	rgb palette_color( int y_pos, int index);
	void palette_color( int y_pos, int index, rgb color );
	void palette_color( int y_pos, int index, u8 r, u8 g, u8 b );
	
### palette_split
	void palette_split( int y_pos );

### remove_palette_split
	void remove_palette_split( int y_pos );

###	brightest_color
	int brightest_color();

### darkest_color
	int darkest_color();
	
### rgb9
	rgb rgb9( u16 xrgb ); // 0x000 - 0x777
	rgb rgb9( u8 r, u8 g, u8 b ); // 0x0 - 0x7

### rgb12
	rgb rgb12( u16 xrgb ); // 0x000 - 0xfff
	rgb rgb12( u8 r, u8 g, u8 b );  // 0x0 - 0xf

### rgb18
	rgb rgb18( u32 xrgb );  // 0x000000 - 0x3f3f3f
	rgb rgb18( u8 r, u8 g, u8 b ); // 0x00 - 0x3f
	

## Misc	

### now
	enum day_id { DAY_MONDAY, DAY_TUESDAY, DAY_WEDNESDAY, DAY_THURSDAY, DAY_FRIDAY, DAY_SATURDAY, DAY_SUNDAY, };
	struct datetime final { int year; int month; int day; int hour; int minute; int second; day_id day_of_week; };
	datetime now();

### time		
	i64 time();

### clock
	int clock();

### delta_time
	float delta_time();

### mem_used
	size_t mem_used( int* alloc_count = 0, size_t* peak_use = 0 );

###	mem_alloc
	void* mem_alloc( size_t size );

###	mem_free
	void mem_free( void* ptr );

### title
	void title( string const& text );
	string title();

### window_pos
	void window_pos( int x, int y );

### window_x
	int window_x();

### window_y
	int window_y();

### window_size
	void window_size( int width, int height );

### window_width
	int window_width();

### window_height
	int window_height();

### border_size
	void border_size( int width, int height );

### border_width
	int border_width();

### border_height
	int border_height();

### screen_size
	void screen_size( int width, int height );

### screen_width
	int screen_width();

### screen_height
	int screen_height();

### fullscreen
	void fullscreen( bool use_fullscreen );
	bool fullscreen();

### crt_mode
	void crt_mode( bool use_crt );
	bool crt_mode();
	
### displays
	struct display { string id; int x; int y; int width; int height; };
	array<display> const& displays();
	
### file_exists
	bool file_exists( string const& filename );
	

### dir
	struct dir_info final { string filename; bool is_folder; size_t size; };
	array<dir_info> dir( string const& path, bool recursive = false );


## Random number generation
	
### random_seed
	void random_seed( u32 seed );

### random
	float random();
	int random( int min, int max );

### random_bell_curve
	float random_bell_curve( int iterations = 3 );
	int random_bell_curve( int min, int max, int iterations = 3 );

### shuffle
	template< typename T > void shuffle( T* elements, int count );
	template< typename T > void shuffle( array<T>* arr );
	template< typename T > void shuffle( pod_array<T>* arr );

### random_unit_vector
	float3 random_unit_vector();
	

### perlin_noise	
	// This function computes a random value at the coordinate (x,y,z). Adjacent random values are continuous but the noise 
	// fluctuates its randomness with period 1, i.e. takes on wholly unrelated values at integer points. 
	// The "wrap" parameters can be used to create wraparound noise that wraps at powers of two. Specify 0 to mean "don't 
	// care". (The noise always wraps every 256 due details of the implementation, even if you ask for larger or no 
	// wrapping.)
	
	float perlin_noise( float3 pos, int wrap_x = 0, int wrap_y = 0, int wrap_z = 0 );
	float perlin_noise( float x, float y, float z, int wrap_x = 0, int wrap_y = 0, int wrap_z = 0 );
	
### fractal_noise_ridge	
	// Three common fractal noise functions, which produce a wide variety of nice effects depending on the parameters: 
	//     octaves    - number of "octaves" of perlin_noise to sum
	//     lacunarity - spacing between successive octaves (use exactly 2.0 for wrapping output)
	//     gain       - relative weighting applied to each successive octave
	//     offset     - used to invert the ridges, may need to be larger, not sure
	
	float fractal_noise_ridge( float3 pos, float lacunarity = 2.0f, float gain = 0.5f, float offset = 1.0f, int octaves = 6, 
		int wrap_x = 0, int wrap_y = 0, int wrap_z = 0 );
	
	float fractal_noise_ridge( float x, float y, float z, float lacunarity = 2.0f, float gain = 0.5f, float offset = 1.0f, int octaves = 6, 
		int wrap_x = 0, int wrap_y = 0, int wrap_z = 0 );

### fractal_noise_fbm
	float fractal_noise_fbm( float3 pos, float lacunarity = 2.0f, float gain = 0.5f, int octaves = 6, 
		int wrap_x = 0, int wrap_y = 0, int wrap_z = 0 );
	
	float fractal_noise_fbm( float x, float y, float z, float lacunarity = 2.0f, float gain = 0.5f, int octaves = 6, 
		int wrap_x = 0, int wrap_y = 0, int wrap_z = 0 );
		
### fractal_noise_turbulence
	float fractal_noise_turbulence( float3 pos, float lacunarity = 2.0f, float gain = 0.5f, int octaves = 6, 
		int wrap_x = 0, int wrap_y = 0, int wrap_z = 0 );
	
	float fractal_noise_turbulence( float x, float y, float z, float lacunarity = 2.0f, float gain = 0.5f, int octaves = 6, 
		int wrap_x = 0, int wrap_y = 0, int wrap_z = 0 );

## binary 

	struct binary { size_t size; void* data; };
	
### bload
	ref<binary> bload( string const& filename );

### bnew
	ref<binary> bnew( size_t size );

### bresize
	ref<binary> bresize( ref<binary> const& bin, size_t new_size );
	
### bsave
	void bsave( void* data, size_t size, string filename );
	void bsave( ref<binary> const& bin, string filename );
	
## Binary reader

### Construction
	struct binary_reader final
		{
		binary_reader( ref<binary> const& bin );
		~binary_reader();
		
### binary_reader.bin
		ref<binary> bin();

### binary_reader.reset
		void reset();

### binary_reader.position
		void position( size_t pos );
		size_t position() const;
		
### binary_reader.read
		int read( char* value, int count = 1 );
		int read( i8* value, int count = 1 );
		int read( i16* value, int count = 1 );
		int read( i32* value, int count = 1 );
		int read( i64* value, int count = 1 );
		int read( u8* value, int count = 1 );
		int read( u16* value, int count = 1 );
		int read( u32* value, int count = 1 );
		int read( u64* value, int count = 1 );
		int read( float* value, int count = 1 );
		int read( double* value, int count = 1 );
		int read( bool* value, int count = 1 ); 
	
## Binary writer
	
### Construction
	struct binary_writer final
		{
		binary_writer( ref<binary> const& bin, bool resize = true );
		~binary_writer();
		
### binary_writer.bin
		ref<binary> bin();

### binary_writer.reset
		void reset();

### binary_writer.position
		void position( size_t pos );
		size_t position() const;
		
### binary_writer.write
		int write( char const* value, int count = 1 );
		int write( i8 const* value, int count = 1 );
		int write( i16 const* value, int count = 1 );
		int write( i32 const* value, int count = 1 );
		int write( i64 const* value, int count = 1 );
		int write( u8 const* value, int count = 1 );
		int write( u16 const* value, int count = 1 );
		int write( u32 const* value, int count = 1 );
		int write( u64 const* value, int count = 1 );
		int write( float const* value, int count = 1 );
		int write( double const* value, int count = 1 );
		int write( bool const* value, int count = 1 );  
		
	
## ini files

	typedef dictionary<string_id, string> ini_section;
	
	struct ini_file
		{
		ini_section global;
		dictionary<string_id, ini_section> sections;
		};
	
### ini_load
	ini_file ini_load( ref<binary> const& bin );
	ini_file ini_load( string const& filename );
	
### ini_save

	ref<binary> ini_save( ini_file const& ini );
	void ini_save( ini_file const& ini, string const& filename );




## array

	template< typename T, int CAPACITY = 16 > struct array final : array_ns::array_type< T, CAPACITY, array_ns::NOT_POD >
		{ 
		explicit array( int initial_capacity = CAPACITY );
		template< typename U > array( U const& other );
		template< typename U > explicit array( U const* items, int count );
		};
	
	
	template< typename T, int CAPACITY = 16 > struct pod_array final : array_ns::array_type< T, CAPACITY, array_ns::IS_POD >
		{ 
		explicit pod_array( int initial_capacity = CAPACITY );
		template< typename U > pod_array( U const& other );
		template< typename U > explicit pod_array( U const* items, int count );
		};
	
	
	enum POD_ENUM { IS_POD, NOT_POD };
	
	
	
	template< typename T, int CAPACITY = 16 > 
	struct array : array_type< T, CAPACITY, NOT_POD >
		{ 
	    explicit array( int initial_capacity = CAPACITY, void* memctx = 0 ) : array_type<T, CAPACITY, NOT_POD>( initial_capacity, memctx ) {}
		template< typename U > array( U const& other ) : array_type<T, CAPACITY, NOT_POD>( other ) {}
		template< typename U > array( U const* items, int count, void* memctx = 0 ) : array_type<T, CAPACITY, NOT_POD>( items, count, memctx ) {}
	    };
	
	
	template< typename T, int CAPACITY = 16 > 
	struct pod_array : array_type< T, CAPACITY, IS_POD >
		{ 
		explicit pod_array( int initial_capacity = CAPACITY, void* memctx = 0 ) : array_type<T, CAPACITY, IS_POD>( initial_capacity, memctx ) {}
		template< typename U > pod_array( U const& other ) : array_type<T, CAPACITY, IS_POD>( other ) {}
		template< typename U > pod_array( U const* items, int count, void* memctx = 0  ) : array_type<T, CAPACITY, IS_POD>( items, count, memctx ) {}
	    };
	

	template< typename T, int CAPACITY, POD_ENUM POD > 
	struct array_type
		{
		explicit array_type( int initial_capacity = CAPACITY, void* memctx = 0 );
		~array_type();
	
		array_type( array_type<T, CAPACITY, POD> const& other );
		template< typename U > array_type( U const& other );
		template< typename U > array_type( U const* items, int count, void* memctx = 0  );
	
		array_type<T, CAPACITY, POD> const& operator= ( array_type<T, CAPACITY, POD> const& other );
		template< typename U > array_type<T, CAPACITY, POD> const& operator= ( U const& other );

### array.add	
		T& add( T const& item);
		T& add();

### array.insert
		T& insert( int index, T const& item);
		T& insert( int index );

### array.set
		void set( int index, T const& item);

### array.remove
		void remove( int index );

### array.unordered_remove
	    void unordered_remove( int index );

### array.clear
		void clear();

### array.resize
		void resize( int new_count, T const& item );
		void resize( int new_count );	

### array.capacity_set
		void capacity_set( int capacity ) const;
	
### array.capacity
		int capacity() const;
		int count() const;

### array.get	
		T& get( int index );
		T const& get( int index ) const;
	
### array.operator[]
		T& operator[]( int index );
		T const& operator[]( int index ) const;

### array.data	
		T* data();
		T const* data() const;
	
### array.contains
		bool contains( T const& item ) const;
					

## dictionary
	
	template<typename KEY, typename T> struct entry_t 
		{
		KEY key;
		T value;
		};
			

	template< typename KEY, typename T, int CAPACITY = 16 > 
	struct dictionary : array_ns::array_type< entry_t<KEY, T>, CAPACITY, array_ns::NOT_POD >
		{ 
		explicit dictionary() : 
			array_ns::array_type<entry_t<KEY, T>, CAPACITY, array_ns::NOT_POD>( CAPACITY, pixie::internal::memctx() )

		template< typename U > dictionary( U const& other ) : 
			array_ns::array_type<entry_t<KEY, T>, CAPACITY, array_ns::NOT_POD>( other ) 

		template< typename U > explicit dictionary( U const* items, int count ) :
			array_ns::array_type<entry_t<KEY, T>, CAPACITY, array_ns::NOT_POD>( items, count ) 
			
### dictionary.find	
	    T const* find( KEY const& key ) const	
	    T* find( KEY const& key ) 

### dictionary.remove	
	    void remove( KEY const& key ) 
	        
### dictionary.key			
		KEY const& key( int index ) const

### dictionary.item	
		T& item( int index )
	
		T const& item( int index ) const

### dictionary.operator[]	
	    T& operator[]( KEY const& key )
	

## Memory pool

	template< typename T > struct mempool final
	    {
	    mempool( int initial_capacity = 256, void* memctx = 0 );
	    ~mempool();

### mempool.create
	    inline T* create();
		template< typename P0 > T* create( P0 p0 );

		template< typename P0, typename P1, typename P2, typename P3, typename P4, typename P5, typename P6, typename P7, typename P8, typename P9 > T* create( P0 p0, P1 p1, P2 p2, P3 p3, P4 p4, P5 p5, P6 p6, P7 p7, P8 p8, P9 p9 );

### mempool.destroy		
	void destroy( T* );

### mempool.clear	
    void clear();

### mempool.contains
    bool contains( T* item );

### mempool.allocate		
    T* allocate();

### mempool.deallocate
    void deallocate( T* ptr );

	
### pool_ref

	template< typename T > ref<T> pool_ref( mempool<T>* pool_instance );
	template< typename T, typename P0 > ref<T> pool_ref( mempool<T>* pool_instance, P0 p0 );

	template< typename T, typename P0, typename P1, typename P2, typename P3, typename P4, typename P5, typename P6, typename P7, typename P8, typename P9 > ref<T> pool_ref( mempool<T>* pool_instance, P0 p0, P1 p1, P2 p2, P3 p3, P4 p4, P5 p5, P6 p6, P7 p7, P8 p8, P9 p9 );

## Sorting and searching

### swap
	template< typename T > void swap( T* a, T* b );

### sort
	template< typename T > void sort( T* elements, int count );
	template< typename T > void sort( array<T>* arr );
	template< typename T > void sort( pod_array<T>* arr );
	
### custom_sort
	template< typename T, int (*COMPARE_FUNC)( T const&, T const& ) > void custom_sort( T* array, int count );
	template< typename T, int (*COMPARE_FUNC)( T const&, T const& ) > void custom_sort( array<T>* arr );
	template< typename T, int (*COMPARE_FUNC)( T const&, T const& ) > void custom_sort( pod_array<T>* arr );

### find	
	template< typename T > int find( T const* haystack_elements, int haystack_count, T const& needle );
	template< typename T > int find( array<T> const& haystack, T const& needle );
	template< typename T > int find( pod_array<T> const& haystack, T const& needle );


## Resources

### mount_resources
	void mount_resources( string const& path );
	void mount_resources( string const& primary, string const& secondary );
	
### resource
	template< typename T > struct resource final : resources::resource<T>
		{
		resource() : resources::resource<T>( internal::resource_system() ) {} ;
		template< typename P0 > resource( P0 const& p0 ) : resources::resource<T>( internal::resource_system(), p0 ) {} ;
		template< typename P0, typename P1 > resource( P0 const& p0, P1 const& p1 ) : resources::resource<T>( internal::resource_system(), p0, p1 ) {} ;
	
		template< typename P0, typename P1, typename P2, typename P3, typename P4, typename P5, typename P6, typename P7, typename P8, typename P9 > resource( P0 const& p0, P1 const& p1, P2 const& p2, P3 const& p3, P4 const& p4, P5 const& p5, P6 const& p6, P7 const& p7, P8 const& p8, P9 const& p9 ) : resources::resource<T>( internal::resource_system(), p0, p1, p2, p3, p4, p5, p6, p7, p8, p9 ) {} ;
		};
	
###	pin_resource

	template< typename T > void pin_resource( resource< T > const& res, string_id group = "" );

###	unpin_resource

	template< typename T > void unpin_resource( resource< T > const& res, string_id group = "" );
	void unpin_resource( string_id group = "" );
	void unpin_resources();



## audio

### Construction
	struct audio final
		{
		audio();
		explicit audio( ref<binary> bin );        
		explicit audio( float* sample_pairs, int sample_pairs_count, bool take_ownership_of_memory = false );
		~audio();

### audio.length	
		float length() const;
				

### audio.create_instance
		audio_instance* create_instance();

### audio.destroy_instance
		void destroy_instance( audio_instance* instance );
	
## Audio

### master_volume
	void master_volume( float volume );
	float master_volume();
	
### pause_audio
	void pause_audio();

### resume_audio
	void resume_audio();


## Music
	
### play_music
	void play_music( resource<audio> audio_resource, float fade_in_time = 0.0f, float delay = 0.0f );

### stop_music
	void stop_music( float fade_out_time = 0.5f );

### pause_music
	void pause_music();

### resume_music 
	void resume_music();

### switch_music  
	void switch_music( resource<audio> audio_resource, float fade_out_time = 0.5f, float fade_in_time = 0.0f, float delay = 0.0f );

### cross_fade_music  
	void cross_fade_music( resource<audio> audio_resource, float cross_fade_time = 1.0f, float delay = 0.0f );

### music_position  
	void music_position( float position );
	float music_position();
	
### current_music  
	resource<audio> current_music();
		
### music_loop  
	void music_loop( bool loop );
	bool music_loop();
		
### music_volume  
	void music_volume( float volume );
	float music_volume();
		
### music_pan  
	void music_pan( float pan );
	float music_pan();

## Ambience

### play_ambience   
	void play_ambience( resource<audio> audio_resource, float fade_in_time = 0.0f, float delay = 0.0f );
	
### stop_ambience   
	void stop_ambience( float fade_out_time = 0.5f );
	
### pause_ambience   
	void pause_ambience();
	
### resume_ambience   
	void resume_ambience();
	
### switch_ambience   
	void switch_ambience( resource<audio> audio_resource, float fade_out_time = 0.5f, float fade_in_time = 0.0f, float delay = 0.0f );
	
### cross_fade_ambience   
	void cross_fade_ambience( resource<audio> audio_resource, float cross_fade_time = 1.0f, float delay = 0.0f );
	
### ambience_position   
	void ambience_position( float position );
	float ambience_position();

### current_ambience   
	resource<audio> current_ambience();
	
### ambience_loop   
	void ambience_loop( bool loop );
	bool ambience_loop();
	
### ambience_volume   
	void ambience_volume( float volume );
	float ambience_volume();
	
### ambience_pan   
	void ambience_pan( float pan );
	float ambience_pan();
	

## Sound

### play_sound    
	sound play_sound( resource<audio> audio_resource, float priority = 0.0f, float fade_in_time = 0.0f, float delay = 0.0f );

### sound.loop
		sound loop( bool loop );
		bool loop();

### sound.volume
		sound volume( float volume );
		float volume();

### sound.pan
		sound pan( float pan );
		float pan();

### sound.stop
		sound stop( float fade_out_time = 0.5f );

### sound.pause
		sound pause();

### sound.resume
		sound resume();

### sound.position
		sound position( float pos );
		float position();
	
### sound.audio_resource
		resource<audio> audio_resource();

	

## Custom Audio Formats	
	struct audio_instance
		{
		void (*release)( audio_instance* instance );
		int (*read_samples)( audio_instance* instance, float* sample_pairs, int sample_pairs_count );
		void (*restart)( audio_instance* instance );
		void (*set_position)( audio_instance* instance, int offset_in_sample_pairs_from_start );
		int (*get_position_in_sample_pairs_from_start)( audio_instance* instance );
		int (*get_length_in_sample_pairs)( audio_instance* instance );
		};
	
	typedef audio_instance* (*audio_format_t)( void* data, size_t size );
	
	void register_audio_format( audio_format_t format );
	

## Graphics
	
### screen_bitmap
	bitmap* screen_bitmap();
	
### 
	struct point final { int x; int y; };
	enum text_align { TEXT_ALIGN_LEFT, TEXT_ALIGN_RIGHT, TEXT_ALIGN_CENTER, };

### clear
	void clear();
	void clear( bitmap* target );

### fill
	void fill( int color );
	void fill( bitmap* target, int color );
	
### pset
	void pset( int x, int y, int color );
	void pset( bitmap* target, int x, int y, int color );
		
### line
	void line( int x1, int y1, int x2, int y2, int color );
	void line( bitmap* target, int x1, int y1, int x2, int y2, int color );
		
### box
	void box( int x1, int y1, int x2, int y2, int color );
	void box( bitmap* target, int x1, int y1, int x2, int y2, int color );
	
### box_fill
	void box_fill( int x1, int y1, int x2, int y2, int color );
	void box_fill( bitmap* target, int x1, int y1, int x2, int y2, int color );
		
### circle
	void circle( int x, int y, int r, int color );
	void circle( bitmap* target, int x, int y, int r, int color );
		
### circle_fill
	void circle_fill( int x, int y, int r, int color );
	void circle_fill( bitmap* target, int x, int y, int r, int color );
		
### ellipse
	void ellipse( int x, int y, int rx, int ry, int color );
	void ellipse( bitmap* target, int x, int y, int rx, int ry, int color );
		
### ellipse_fill
	void ellipse_fill( int x, int y, int rx, int ry, int color );
	void ellipse_fill( bitmap* target, int x, int y, int rx, int ry, int color );
		
### polygon
	void polygon( point const* points, int count, int color );
	void polygon( bitmap* target, point const* points, int count, int color );
		
### polygon_fill
	void polygon_fill( point const* points, int count, int color );
	void polygon_fill( bitmap* target, point const* points, int count, int color );
		
### text
	void text( int x, int y, string const& str, int color, resource<font> const& font_resource, 
		text_align align = TEXT_ALIGN_LEFT, int wrap_width = -1, int hspacing = 0, int vspacing = 0, int limit = -1, 
		bool bold = false, bool italic = false, bool underlined = false );
	void text( bitmap* target, int x, int y, string const& str, int color, resource<font> const& font_resource, 
		text_align align = TEXT_ALIGN_LEFT, int wrap_width = -1, int hspacing = 0, int vspacing = 0, int limit = -1, 
		bool bold = false, bool italic = false, bool underlined = false );
		
### text_bounds
	void text_bounds( int x, int y, string const& str, resource<font> const& font_resource, 
		text_align align = TEXT_ALIGN_LEFT, int wrap_width = -1, int hspacing = 0, int vspacing = 0, bool bold = false, 
		bool italic = false, int* width = 0, int* height = 0);

## Bitmap	
		
### load_bitmap
			ref<bitmap> load_bitmap( string const& filename );
	
### Construction
	struct bitmap final
		{
		bitmap();
	
		explicit bitmap( int width, int height );
		explicit bitmap( int width, int height, u8* pixels, int transparent_index = -1 );
		explicit bitmap( int width, int height, u8* pixels, u8* mask );
	
		explicit bitmap( int width, int height, int offset_x, int offset_y, int pitch_x, int pitch_y );
		explicit bitmap( int width, int height, int offset_x, int offset_y, int pitch_x, int pitch_y, u8* pixels, int transparent_index = -1 );
		explicit bitmap( int width, int height, int offset_x, int offset_y, int pitch_x, int pitch_y, u8* pixels, u8* mask );
	
		explicit bitmap( int width, int height, int cel_count );
		explicit bitmap( int width, int height, int cel_count, u8* pixels[], int transparent_index = -1 );
		explicit bitmap( int width, int height, int cel_count, u8* pixels[], u8* masks[] );
	
		explicit bitmap( int width, int height, int cel_count, int offsets_x[], int offsets_y[], int pitches_x[], int pitches_y[] );
		explicit bitmap( int width, int height, int cel_count, int offsets_x[], int offsets_y[], int pitches_x[], int pitches_y[], u8* pixels[], int transparent_index = -1 );
		explicit bitmap( int width, int height, int cel_count, int offsets_x[], int offsets_y[], int pitches_x[], int pitches_y[], u8* pixels[], u8* masks[] );
	
		~bitmap();
	
		bitmap( bitmap const& other );
		bitmap const& operator=( bitmap const& other ); 
		
### bitmap.cel_count
		int cel_count() const;
		
### bitmap.width
		int width() const;
				
### bitmap.height
		int height() const;
	
				
### bitmap.pixel
		void pixel( int cel, int x, int y, int color );
		int pixel( int cel, int x, int y ) const;
			
				
### bitmap.mask
		void mask( int cel, int x, int y, bool opaque );
		bool mask( int cel, int x, int y ) const;
	
				
### bitmap.lock
		struct lock_data final
			{
			int offset_x;
			int offset_y;
			int pitch_x;
			int pitch_y;
			u8* pixels;
			u8* mask;
			};
			
		void lock( lock_data* data );
		void lock( int cel, lock_data* data );
				
### bitmap.unlock
		void unlock();
		
				
### bitmap.blit
		void blit( bitmap* target, int x = 0, int y = 0 );
		void blit( int x1, int y1, int x2, int y2, bitmap* target, int x = 0, int y = 0 );
	
		void blit( int cel, bitmap* target, int x = 0, int y = 0 );
		void blit( int cel, int x1, int y1, int x2, int y2, bitmap* target, int x = 0, int y = 0 );
		
				
### bitmap.offset_x
		int offset_x( int cel = 0 ) const;
				
### bitmap.offset_y
		int offset_y( int cel = 0 ) const;
				
### bitmap.pitch_x
		int pitch_x( int cel = 0 ) const;
				
### bitmap.pitch_y
		int pitch_y( int cel = 0 ) const;
	
	
		
## font

### Construction
	struct font final
		{
		font();
		explicit font( void const* data, size_t size );
		~font();
		
		font( font const& other );
		font const& operator=( font const& other ); 
	
### font.height
		int height() const;

### font.line_spacing
		int line_spacing() const;

### font.baseline
		int baseline() const;
	
### font.blit
		struct bounds_t final { int width; int height; };
	
		enum alignment_enum
			{
			ALIGNMENT_LEFT,
			ALIGNMENT_RIGHT,
			ALIGNMENT_CENTER,
			};
	
		void blit( int x, int y, string const& str, u8 color, u8* target, int width, int height, 
			alignment_enum align = ALIGNMENT_LEFT, int wrap_width = -1, int hspacing = 0, int vspacing = 0, int limit = -1, 
			bool bold = false, bool italic = false, bool underlined = false, bounds_t* bounds = 0 ) const;
		
		void blit( int x, int y, string const& str, u32 color, u32* target, int width, int height, 
			alignment_enum align = ALIGNMENT_LEFT, int wrap_width = -1, int hspacing = 0, int vspacing = 0, int limit = -1, 
			bool bold = false, bool italic = false, bool underlined = false, bounds_t* bounds = 0 ) const;


	
## Sprite manager
	

### sprite_manager Construction
	struct sprite_manager final
		{
		sprite_manager();
		~sprite_manager();
	
### sprite_manager.add
		sprite_manager& add( internal_sprite* spr );


### sprite_manager.remove
		sprite_manager& remove( internal_sprite* spr );
	
### sprite_manager.count
		int count() const;

### sprite_manager.sprite
		internal_sprite* sprite( int index ) const;
	
### sprite_manager.add_event_handler
		void add_event_handler( event_handler* handler );

### sprite_manager.remove_event_handler
		void remove_event_handler( event_handler* handler );
	
### sprite_manager.origin
		sprite_manager& origin( float x, float y );
	
### sprite_manager.origin_x
		sprite_manager& origin_x( float x );
		float origin_x() const;
	
### sprite_manager.origin_y
		sprite_manager& origin_y( float y );
		float origin_y() const;


	
## sprite
	

### sprite Construction
	template< typename T> struct sprite_base : internal_sprite
		{
		sprite_base() { }
		explicit sprite_base( sprite_manager* manager ) : internal_sprite( manager ) { } 
		virtual ~sprite_base() { }
		
		sprite_base( sprite_base const& other ) : internal_sprite( other ) { }
		sprite_base const& operator=( sprite_base const& other ) { internal_sprite::operator=( other ); return *this; }
		
### sprite.manager
		sprite_manager* manager() const { return internal_manager(); }
	
### sprite.position
		virtual T& position( float x, float y ) { return *(T*)&internal_position( x, y ); }
	
	
### sprite.position_x
		virtual T& position_x( float x ) { return *(T*)&internal_position_x( x ); }
		virtual float position_x() const { return internal_position_x(); }
	
	
### sprite.position_y
		virtual T& position_y( float y ) { return *(T*)&internal_position_y( y ); }
		virtual float position_y() const { return internal_position_y(); }
	
	
### sprite.origin
		virtual T& origin( float x, float y ) { return *(T*)&internal_origin( x, y ); }
	
	
### sprite.origin_x
		virtual T& origin_x( float x ) { return *(T*)&internal_origin_x( x ); }
		virtual float origin_x() const { return internal_origin_x(); }
	
	
### sprite.origin_y
		virtual T& origin_y( float y ) { return *(T*)&internal_origin_y( y ); }
		virtual float origin_y() const { return internal_origin_y(); }
		
	
### sprite.visible
		virtual T& visible( bool is_visible ) { return *(T*)&internal_visible( is_visible ); }
		virtual bool visible() const { return internal_visible(); }
	
	
### sprite.zorder
		virtual T& zorder( float z ) { return *(T*)&internal_zorder( z ); }
		virtual float zorder() const { return internal_zorder(); }
	
	
### sprite.cel
		virtual T& cel( float index ) { return *(T*)&internal_cel( index ); };
		virtual float cel() const { return internal_cel(); }
	
	
### sprite.selection_index
		virtual T& selection_index( int index ) { return *(T*)&internal_selection_index( index ); };
		virtual int selection_index() const { return internal_selection_index(); }
	
	
### sprite.bitmap
		virtual T& bitmap( resource<pixie::bitmap> const& bitmap_resource ) { return *(T*)&internal_bitmap( bitmap_resource ); }
		virtual resource<pixie::bitmap> const& bitmap() const { return internal_bitmap(); }
	
	
### sprite.event
		virtual T& event( string_id const& event_id, void* user_data = 0 ) { return *(T*)&internal_event( event_id, user_data ); }
	
	
### sprite.event_id
		virtual T& event_id( string_id const& event_id ) { return *(T*)&internal_event_id( event_id ); }
		virtual string_id const& event_id() const { return internal_event_id(); }
	
	
### sprite.event_user_data
		virtual T& event_user_data( void* user_data ) { return *(T*)&internal_event_user_data( user_data ); }
		virtual void* event_user_data() const { return internal_event_user_data(); }
	
		};
	
	
## rendercall
	
### rendercall Construction
	struct rendercall_handler 
		{
		virtual void render( rendercall* call, bitmap* target ) = 0;
		};
	
	struct rendercall final : sprite_base<rendercall>
		{
		rendercall();
		explicit rendercall( sprite_manager* manager );
		virtual ~rendercall();
		
		rendercall( rendercall const& other );
		rendercall const& operator=( rendercall const& other );
	
### rendercall.handler
		virtual rendercall& handler( rendercall_handler* new_handler );
		virtual rendercall_handler* handler() const;
	

	
## label

### label Construction
	struct label final : sprite_base<label>
		{
		label();
		explicit label( sprite_manager* manager );
		virtual ~label();
		
		label( label const& other );
		label const& operator=( label const& other );
	
### label.text
		virtual label& text( string str );
		virtual string text() const;
	
### label.font
		virtual label& font( resource<pixie::font> const& font_resource );
		virtual resource<pixie::font> const& font() const;
	
	
### label.color
		virtual label& color( int index );
		virtual int color() const;
	
	
### label.wrap
		virtual label& wrap( int width = -1 );
		virtual int wrap() const;
	
	
### label.hspacing
		virtual label& hspacing( int spacing );
		virtual int hspacing() const;
		
	
### label.vspacing
		virtual label& vspacing( int spacing );
		virtual int vspacing() const;
	
	
### label.limit
		virtual label& limit( float lim );
		virtual float limit() const;
		
	
### label.align
		virtual label& align( text_align new_align );
		virtual text_align align() const;
		
	
### label.bold
		virtual label& bold( bool use_bold );
		virtual bool bold() const;
	
	
### label.italic
		virtual label& italic( bool use_italic );
		virtual bool italic() const;
	
	
### label.underline
		virtual label& underline( bool use_underline );
		virtual bool underline() const;
	
	
### label.outline
		virtual label& outline( int color ); // no outline: color = -1
		virtual int outline() const;
	
		
### label.shadow
		virtual label& shadow( int color = -1, float offset_x = 1.0f, float offset_y = 1.0f );
	
	
### label.shadow_color
		virtual label& shadow_color( int color );
		virtual int shadow_color() const;
	
	
### label.shadow_offset_x
		virtual label& shadow_offset_x( float offset );
		virtual float shadow_offset_x() const;
	
	
### label.shadow_offset_y
		virtual label& shadow_offset_y( float offset );
		virtual float shadow_offset_y() const;
		
	
### label.width
		virtual int width() const;

	
### label.height
		virtual int height() const;
		

## button
	
	
### button Construction
	struct button final : sprite_base<button>
		{
		button();
		explicit button( sprite_manager* manager );
		virtual ~button();
		
		button( button const& other );
		button const& operator=( button const& other );
	
	
### button.enabled
		virtual button& enabled( bool is_enabled ); 
		virtual bool enabled() const;
	
	
### button.bitmap_disabled
		virtual button& bitmap_disabled( resource<pixie::bitmap> const& bitmap_resource );
		virtual resource<pixie::bitmap> const& bitmap_disabled() const;
	
	
### button.bitmap_highlighted
		virtual button& bitmap_highlighted( resource<pixie::bitmap> const& bitmap_resource );
		virtual resource<pixie::bitmap> const& bitmap_highlighted() const;
	
	
### button.bitmap_activated
		virtual button& bitmap_activated( resource<pixie::bitmap> const& bitmap_resource );
		virtual resource<pixie::bitmap> const& bitmap_activated() const;
	
	
### button.pixel_picking
		virtual button& pixel_picking( bool use_pixel_picking );
		virtual bool pixel_picking() const;
	
	
	
### button.simulate_activated
		virtual void simulate_actived( float pressed_time = 0.15f );
	

	

## shape
	
	
### shape Construction

	struct shape final : sprite_base<shape>
		{
		shape();
		explicit shape( sprite_manager* manager );
		virtual ~shape();
		
		shape( shape const& other );
		shape const& operator=( shape const& other );
	
	
### shape.filled
		virtual shape& filled( bool is_filled );
		virtual bool filled() const;
	
	
### shape.color
		virtual shape& color( int index );
		virtual int color() const;
	
	
### shape.point
		virtual shape& point( float x, float y );
	
### shape.line
		virtual shape& line( float x1, float y1, float x2, float y2 );
		virtual shape& line( float x2, float y2 );
	
### shape.box
		virtual shape& box( float x1, float y1, float x2, float y2 );
		virtual shape& box( float width, float height );
	
### shape.circle
		virtual shape& circle( float x, float y, float r );
		virtual shape& circle( float r );
	
### shape.ellipse
		virtual shape& ellipse( float x, float y, float rx, float ry );
		virtual shape& ellipse( float rx, float ry );
	
### shape.polygon
		virtual shape& polygon( int count, float const* points );
	
	
### shape.shape_data
		enum shape_type { SHAPE_TYPE_NONE, SHAPE_TYPE_POINT, SHAPE_TYPE_LINE, SHAPE_TYPE_BOX, SHAPE_TYPE_CIRCLE, 
			SHAPE_TYPE_ELLIPSE, SHAPE_TYPE_POLYGON, };
		
		struct shape_data
			{
			shape_type type;
			union 
				{
				struct { float x; float y; } point;
				struct { float x1; float y1; float x2; float y2; } line;
				struct { float x1; float y1; float x2; float y2; } box;
				struct { float x; float y; float r; } circle;
				struct { float x; float y; float rx; float ry; } ellipse;
				struct { int count; float const* points; } polygon;
				} data;
			};
	
		virtual shape& data( shape_data const& data );
		virtual shape_data const& data() const;
	
 

## funccall

	struct func_call
	{
	virtual void operator()() = 0;
	virtual void call() = 0;
	virtual ~func_call() { }
	};
	
	
	// lambda
	
	template< typename F > 
	refcount::ref<func_call> make_func_call( F f ); 
	
	template< typename F, typename P0 > 
	refcount::ref<func_call> make_func_call( F f, P0 p0 ); 
	

	template< typename F, typename P0, typename P1, typename P2, typename P3, typename P4, typename P5, typename P6, typename P7, typename P8, typename P9 > 
	refcount::ref<func_call> make_func_call( F f, P0 p0, P1 p1, P2 p2, P3 p3, P4 p4, P5 p5, P6 p6, P7 p7, P8 p8, P9 p9 ); 
	
	
	// cdeclcall
	
	template< typename R >
	refcount::ref<func_call> make_func_call( R (*f)() ); 
	
	template< typename R, typename P0 > 
	refcount::ref<func_call> make_func_call( R (*f)( P0 ), P0 p0 ); 
	
	
	template< typename R, typename P0, typename P1, typename P2, typename P3, typename P4, typename P5, typename P6, typename P7, typename P8, typename P9 > 
	refcount::ref<func_call> make_func_call( R (*f)( P0, P1, P2, P3, P4, P5, P6, P7, P8, P9 ), P0 p0, P1 p1, P2 p2, P3 p3, P4 p4, P5 p5, P6 p6, P7 p7, P8 p8, P9 p9 ); 
	
	
	// thiscall
	
	template< typename T, typename R > 
	refcount::ref<func_call> make_func_call( T* t, R (T::*f)() ); 
	
	template< typename T, typename R, typename P0 > 
	refcount::ref<func_call> make_func_call( T* t, R (T::*f)( P0 ), P0 p0 ); 
	

	template< typename T, typename R, typename P0, typename P1, typename P2, typename P3, typename P4, typename P5, typename P6, typename P7, typename P8, typename P9 > 
	refcount::ref<func_call> make_func_call( T* t, R (T::*f)( P0, P1, P2, P3, P4, P5, P6, P7, P8, P9 ), P0 p0, P1 p1, P2 p2, P3 p3, P4 p4, P5 p5, P6 p6, P7 p7, P8 p8, P9 p9 ); 
	
	
	// thiscall refcount::ref
	
	template< typename T, typename R > 
	refcount::ref<func_call> make_func_call( refcount::ref<T> const& t, R (T::*f)() ); 
	
	template< typename T, typename R, typename P0 > 
	refcount::ref<func_call> make_func_call( refcount::ref<T> const& t, R (T::*f)( P0 ), P0 p0 ); 
	
	
	template< typename T, typename R, typename P0, typename P1, typename P2, typename P3, typename P4, typename P5, typename P6, typename P7, typename P8, typename P9 > 
	refcount::ref<func_call> make_func_call( refcount::ref<T> const& t, R (T::*f)( P0, P1, P2, P3, P4, P5, P6, P7, P8, P9 ), P0 p0, P1 p1, P2 p2, P3 p3, P4 p4, P5 p5, P6 p6, P7 p7, P8 p8, P9 p9 ); 
	 
## Type id
	
	typedef void const* type_id_t;
	template< typename T > type_id_t type_id();
	template< typename T > type_id_t type_id( T const& );


## Tweening
	
### tween

	tweener tween( float duration );

### stop_all_tweeners

	void stop_all_tweeners();

### tweener	

	struct tweener
		{
	    tweener();
	
### tweener.completed
	    bool completed() const;

### tweener.property	
		template< typename T > 
		tweener property( tween::property<T>* prop, T const& target, T (*lerp_func)( T, T, float ) = 0 );#	

		template< typename T > 
		tweener property( refcount::ref< tween::property<T> > const& prop, T const& target, T (*lerp_func)( T, T, float ) = 0  );
	
### tweener.delay
		tweener delay( float duration );

### tweener.ease	
		typedef float (*ease_func_t)( float );
		tweener ease( ease_func_t ease_func );

### tweener.repeat
	    tweener repeat( int times = -1 );

### tweener.pingpong
		tweener pingpong( bool value = true );

### tweener.reverse
		tweener reverse( bool value = true );

### tweener.on_complete		
		tweener on_complete( refcount::ref<funccall::func_call> const& handler );

	    template<typename F > 
	    tweener on_complete( F handler );
	
	    template<typename F, typename P0 > 
	    tweener on_complete( F handler, P0 p0 );
	
	
	    template<typename F, typename P0, typename P1, typename P2, typename P3, typename P4, typename P5, typename P6, typename P7, typename P8, typename P9 > 
	    tweener on_complete( F handler, P0 p0, P1 p1, P2 p2, P3 p3, P4 p4, P5 p5, P6 p6, P7 p7, P8 p8, P9 p9 );
	
	
### tweener.on_repeat
		tweener on_repeat( refcount::ref<funccall::func_call> const& handler );

	    template<typename F > 
	    tweener on_repeat( F handler );
	
	    template<typename F, typename P0 > 
	    tweener on_repeat( F handler, P0 p0 );
	
	    template<typename F, typename P0, typename P1 > 
	    tweener on_repeat( F handler, P0 p0, P1 p1 );
	

	    template<typename F, typename P0, typename P1, typename P2, typename P3, typename P4, typename P5, typename P6, typename P7, typename P8, typename P9 > 
	    tweener on_repeat( F handler, P0 p0, P1 p1, P2 p2, P3 p3, P4 p4, P5 p5, P6 p6, P7 p7, P8 p8, P9 p9 );
	


### tweener.on_update
		tweener on_update( refcount::ref<funccall::func_call> const& handler );
	
	    template<typename F > 
	    tweener on_update( F handler );
	
	    template<typename F, typename P0 > 
	    tweener on_update( F handler, P0 p0 );
		
	    template<typename F, typename P0, typename P1, typename P2, typename P3, typename P4, typename P5, typename P6, typename P7, typename P8, typename P9 > 
	    tweener on_update( F handler, P0 p0, P1 p1, P2 p2, P3 p3, P4 p4, P5 p5, P6 p6, P7 p7, P8 p8, P9 p9 );
		


## Tween helpers

### slowprint

	tweener slowprint( label* lbl, string text, float delay = 0.1f );
	

## property

	template< typename T > struct property
		{
		virtual ~property() { }
		
### property.get	
		virtual T get() const = 0;

### property.set		
	virtual void set( T const& ) = 0;
		};
	
### make_property	
	template< typename T > refcount::ref< property<T> > make_property( T* variable );
	template< typename T > refcount::ref< property<T> > make_property( T (*get)(), void (*set)( T ) );
	
	
	template< typename T, typename U > refcount::ref< property<T> > make_property( T (*get)( U* ), void (*set)( T, U* ), U* usr );
	template< typename T, typename U > refcount::ref< property<T> > make_property( U* instance, T U::*member );
	template< typename T, typename U > refcount::ref< property<T> > make_property( U* instance, T (U::*get)(), void (U::*set)( T ) );
	template< typename T, typename U > refcount::ref< property<T> > make_property( U* instance, T (U::*get)() const, void (U::*set)( T ) );
	template< typename T, typename U > refcount::ref< property<T> > make_property( U* instance, T (U::*get)() const, U& (U::*set)( T ) );
	
	template< typename T, typename U > refcount::ref< property<T> > make_property( T (*get)( U* ), void (*set)( T,  U* ), refcount::ref<U> const& usr );
	template< typename T, typename U > refcount::ref< property<T> > make_property( refcount::ref<U> const& instance, T U::*member );
	template< typename T, typename U > refcount::ref< property<T> > make_property( refcount::ref<U> const& instance, T (U::*get)(), void (U::*set)( T ) );
	template< typename T, typename U > refcount::ref< property<T> > make_property( refcount::ref<U> const& instance, T (U::*get)() const, void (U::*set)( T ) );
	template< typename T, typename U > refcount::ref< property<T> > make_property( refcount::ref<U> const& instance, T (U::*get)() const, U& (U::*set)( T ) );

## Built-in properties

### sprite

- property_position_x
- property_position_y
- property_origin_x
- property_origin_y
- property_visible
- property_zorder
- property_cel
- property_selection_index
- property_bitmap
- property_event_id
- property_event_user_data


### rendercall

- property_handler


### label

- property_text
- property_font
- property_color
- property_wrap
- property_hspacing
- property_vspacing
- property_limit
- property_align
- property_bold
- property_italic
- property_underline
- property_outline
- property_shadow_color
- property_shadow_offset_x
- property_shadow_offset_y


### button

- property_enabled
- property_bitmap_disabled
- property_bitmap_highlighted
- property_bitmap_activated
- property_pixel_picking


### shape

- property_filled( shape* sprite );
- property_color( shape* sprite );
- property_shape_data( shape* sprite );


## Easing 


### Ease functions

- ease_linear
- ease_smoothstep
- ease_smootherstep
- ease_quad_out / ease_quad_in / ease_quad_in_out
- ease_back_out / ease_back_in / ease_back_in_out
- ease_bounce_out / ease_bounce_in / ease_bounce_in_out
- ease_sine_out / ease_sine_in / ease_sine_in_out
- ease_elastic_out / ease_elastic_in / ease_elastic_in_out
- ease_expo_out / ease_expo_in / ease_expo_in_out
- ease_cubic_out /ease_cubic_in / ease_cubic_in_out
- ease_quart_out / ease_quart_in / ease_quart_in_out
- ease_quint_out / ease_quint_in / ease_quint_in_out
- ease_circle_out / ease_circle_in / ease_circle_in_out


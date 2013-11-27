# C(99) preprocessor tricks

On account of a recent project i've been working on, I had the chance to
program a bit of C, something I have not done in a while. 
This is turn led me to think about macros, and how some of the features in C99
came together to help me a bit.


Part of the code involved a message generator that sequentially appended message
data into a buffer.
This started by being a wrapper around *(v)snprintf()* but I disliked it
on account of the easy bugs I kept introducing, trough mismatches between the
format string and the function arguments, or protocol specific errors because I
messed up the message format:

    snprintf(buf, buflen, "411 :%s\r\n"); // This invites crashing

So I was looking for a better(safer) API for this, that:

* Did not lead to accidental SEGFAULT, because I forgot a parameter - those should be compile errors
* Added some correctness checks, e.g. Is the message valid
* A pleasant API would be nice too

My first attempts looked mostly like an *strcat()*

    // Ignore the "to" argument - its the msg destination/buffer
    writebuf_append(to, "411: ");
    writebuf_append(to, some_data);
    writebuf_append(to, frame_terminator);

Yes it looks awful, and we have not gained much other than some safety.

Later on I realised I preferred a function that could do all this in a single
step, in order to be able to check the message for correctness, e.g.

    writebuf_append(to, "411: ", some_data, frame_terminator);

And so I started looking at variadic functions (stdarg.h), but discarded them
after realising that I would need to pass the number of arguments along, i.e.

    writebuf_append(to, 3, "411: ", some_data, frame_terminator); // 3 params

Afterwards I started looking at encapsulating all the arguments in a list of struct, (with a final element guarding the end of the list)

    struct params {
    	char *val;
        size_t len;
    };
    void writebuf_append(to, struct param *params);

C99 initialisers help a bit

    struct param params[] = {
    	{"411", strlen("411")},
        {some_data, some_data_len},
    	{NULL, 0}
    }
    writebuf_append(to, params);

The advantage being that you do it in a single call, and the function can
handle add the message terminator internally.

By this point personal aesthetics start to get in the way, specially if you
think about doing this with half a dozen arguments. You can try to do this
inline, but you will need an additional cast:

    writebuf_append(to, (struct param []){ {"411", strlen("411")}, {some_data, some_data_len}, {NULL, 0} } );

The annoying bits here are the extra cast, the terminator element and the
abundance of brackets. First I decided to create a macro to simplify the initialisation of each individual arguments a bit

    // My data is NULL terminated (adjust accordingly)
    #define PARAM(p) {p, strlen(p)}	
    writebuf_append(to, (struct param []){ PARAM("411"), PARAM(some_data), {NULL, 0} } );

But now I really wanted to hide the cast, brackets and the terminator from the
call - and so I started to look into variadic macros. It turned out they are not
as flexible as I would like, you can't get individual arguments, and there are some comma
related bugs. But from the macro I already had, I can define a macro to wrap
the existing function


    #define writebuf_append2(to, ...) writebuf_append(to, (struct param[]){ __VA_ARGS__, {NULL, 0} } )

And now I can call

    writebuf_append2(to, PARAM("411"), PARAM(some_data) );

which looks more like a regular function call. The PARAM macro might look annoying, but it can be useful if you have different types of arguments.

    // type is an enum I added to the struct
    #define PARAM(arg, type)	{arg,strlen(arg),type}
    // define type specific macros
    #define PARAM_CODE(arg)	PARAM(arg, CODE)
    #define PARAM_MSG(arg)	PARAM(arg, MSG)

    writebuf_append2(to, 
    			PARAM_CODE("411"), 
    			PARAM_MSG(some_data) );

Internally I use the type to add correctness checks and append additional bytes
to the message (e.g. whitespace or field separators) - the previous example
would be the equivalent of doing:

    // Sigh this still *looks* a lot better :D, but the extra feats
    // are worth the bulky wrapping
    snprintf(to, "411 :%s\r\n", some_data)

BTW life with macros becomes much easier with **clang**, because the compiler
explicitly expands the macros when reporting errors.

## References

* [C #define macro for debug printing](http://stackoverflow.com/questions/1644868/c-define-macro-for-debug-printing)


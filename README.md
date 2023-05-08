Download Link: https://assignmentchef.com/product/solved-solved-cse3320-programming-assignment-1-calls-shell
<br>
Description:

You will develop a small “text-based shell utility” (“ts”) for Unix

(or similar OS). A common complaint of the most-used UNIX (text) shells

(Bourne, Korn, C) is that it is difficult to remember (long) file names,    and type “long” command names. A “menu” type shell allows a user to “pick”    an item (file or command)from a “menu”.




You will display a simple menu, the following is a suggestion (you may change format):




Current Working Dir: /home/os.progs/Me

It is now: 28 August 2017, 10:58 PM




<table width="252">

 <tbody>

  <tr>

   <td width="94">  Files:</td>

   <td width="50"></td>

   <td width="108">0. ts.c</td>

  </tr>

  <tr>

   <td width="94"></td>

   <td width="50"></td>

   <td width="108">1. a.out</td>

  </tr>

  <tr>

   <td width="94"></td>

   <td width="50"></td>

   <td width="108">2. ts.txt</td>

  </tr>

  <tr>

   <td width="94"></td>

   <td width="50"></td>

   <td width="108">3. assignment1</td>

  </tr>

  <tr>

   <td width="94"></td>

   <td width="50"></td>

   <td width="108">4. program2.c</td>

  </tr>

 </tbody>

</table>




Directories:     0. ..

<ol>

 <li>             my_dir</li>

</ol>




<table width="343">

 <tbody>

  <tr>

   <td width="139">  Operation:</td>

   <td width="204">E  Edit</td>

  </tr>

  <tr>

   <td width="139"></td>

   <td width="204">R  Run</td>

  </tr>

  <tr>

   <td width="139"></td>

   <td width="204">C  Change Directory</td>

  </tr>

  <tr>

   <td width="139"></td>

   <td width="204">S  Sort Directory listing</td>

  </tr>

  <tr>

   <td width="139"></td>

   <td width="204">Q  Quit</td>

  </tr>

 </tbody>

</table>




You will read a single key “command” entered by the user, then a file or   directory number or partial file name with “completion” and ts will output    some system information on the terminal, or run a program (by means of systems calls).

The commands you will implement:

Edit – opens a text editor with a file.

Run – runs an executable program. You should handle parameters.

Change – changes working directory.

Sort – sorts listing by either size or date (prompt user)

You will need to implement:

Prev, Next, and Operations need to be merged (menu fits on one screen).      Store names in an array, Only one pass through directory.




If the “ts” program has an argument, use it as the directory starting   point (working dir), like: “./ts /bin”.

You should provide a reasonable user interface. (See below)

You may write this in ADA, Assembler, C, C++ or Java.(Others with permission)   You should target ts to Unix, MacOS, (or similar), or discuss alternatives    with instructor.




Constraints:

How many files and directories can you handle (max 1024)

How long is a file name (max 2048 characters, really: limits.h, NAME_MAX)

Bonus:

Show additional file information (date, size, read/execute)       Use file name completion as well as a number.

Add a pull-down menu using terminal codes or curses (ncurses).




/*  Some example code and prototype –

contains many, many problems: should check for return values      (especially system calls), handle errors, not use fixed paths,     handle parameters, put comments, watch out for buffer overflows,     security problems, use environment variables, etc.                  */

#include &lt;sys/types.h&gt;

#include &lt;stdlib.h&gt;

#include &lt;stdio.h&gt;

#include &lt;unistd.h&gt;

#include &lt;dirent.h&gt;

#include &lt;string.h&gt;

#include &lt;time.h&gt;

#include &lt;curses.h&gt;

int main(void) {     pid_t child;     DIR * d;     struct dirent * de;

int i, c, k;

char s[256], cmd[256];                            /* fixed length buffers? */     time_t t;

while (1) {       t = time( NULL );

printf( “Time: %s
”, ctime( &amp;t ));




getcwd(s, 200);      /* why 200? What if bigger? Check for errors? */       printf( “
Current Directory: %s 
”, s);




d = opendir( “.” );            /* errors? More below */         c = 0;

while ((de = readdir(d))){           if ((de-&gt;d_type) &amp; DT_DIR)

printf( ” ( %d Directory:  %s ) 
”, c++, de-&gt;d_name);

}       closedir( d );       d = opendir( “.” );       c = 0;

while ((de = readdir(d))){

if (((de-&gt;d_type) &amp; DT_REG))                                            printf( ” ( %d File:  %s ) 
”, c++, de-&gt;d_name);           if ( ( c % 8 ) == 0 ) {

printf( “Hit N for Next
” );    /* What if only subdirs? */              k = getchar( );   }

}       closedir( d );

printf( “—————————————–
” );       c = getchar( ); getchar( );       switch (c) {

case ‘q’: exit(0); /* quit */         case ‘e’: printf( “Edit what?:” );                   scanf( “%s”, s );                   strcpy( cmd, “pico “);                   strcat( cmd, s );

system( cmd );    /*this is bad, should use fork() then execv() or execl() */                   break;

case ‘r’: printf( “Run what?:” );                   scanf( “%s”, cmd );                   system( cmd );                   break;

case ‘c’: printf( “Change To?:” );                   scanf( “%s”, cmd );

chdir( cmd );   /* what can go wrong ?  */                   break;

}

}

}
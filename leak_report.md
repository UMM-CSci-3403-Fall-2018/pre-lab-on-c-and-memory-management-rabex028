# Leak report

The memory leak seem to have stemmed from allocating memory on line 41 of the program in the strip function.
This memory is never freed, even after the program is finished with the variable it is used for (char* _result_ in strip).

Result is returned to the is_clean function for comparing strings passed to the program as paramaters and their cleaned counterparts.
main calls is_clean, but does not use the variable that allocated memory, so free() should not be used in main.
Instead, free() should be used in is_clean() on the _cleaned_ variable (which points to the address in memory).

An error is caused if every _cleaned_ variable is attempted to be freed. This is presumably caused if an empty string
or a string with only white space is passed as a parameter to strip(). To solve this issue, if the string length of
_cleaned_ is greater than 0, it is freed, otherwise it is not necessary and causes an error.

Implementing the above change and running valgrind showed that this fixed the memory leak.


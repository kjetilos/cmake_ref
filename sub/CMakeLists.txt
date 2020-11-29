
message("Inside subfolder CMakeLists.txt")
cmake_print_variables(MY_VAR)

# This variable has a directory scope, so it should only be visible in
# the current directory CMake context.
set(MY_SUB_VAR "Subfolder Variable")
cmake_print_variables(MY_SUB_VAR)

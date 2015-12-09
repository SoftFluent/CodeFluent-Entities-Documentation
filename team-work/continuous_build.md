# Continuous build

Should I generate the model during the automatic build process?

## Case 1: I check generated files in

If generated files are checked in you don’t need to generate them again, so you may not need to build
the model.

The generating process may in fact do more than just generate code. For instance you can deploy a
database using persistence producers. If those steps are part of your build process, you may want to
build the model even if generated files are checked in.

*Note: If you don’t need all producers, you should use the Configuration Manager as described in §3.2.*

## Case 2: I don’t check generated files in

If you don’t check generated files in you must build the model.


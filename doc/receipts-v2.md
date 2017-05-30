Brief info about SliTaz receipts v2
===================================

Version 2 was developed as an extension of the receipts in order to facilitate
the maintenance of packages by small forces.

In order to switch to version 2, you must specify 'v2' in the first line of the
receipt:

    # SliTaz package receipt v2.

You can write the single receipt v2 to compile, for example `attr` sources and
then make two packages: `attr` and `attr-dev` using compiled files. Next we will
call `attr` the *main package*, while `attr-dev` -- the *split package*.

You must specify all the names of *split packages* that must be created after
the compilation in the SPLIT variable. Example for our `attr` receipt:

    SPLIT="attr-dev"

You must specify rules to generate each package inside the genpkg_rules().
Example for package `attr`:

    genpkg_rules()
    {
        case $PACKAGE in
            attr) copy @std ;;
            attr-dev) copy @dev ;;
        esac
    }

Here, in every rule you can:

  * use the `copy()` function or other methods to copy specified files from
    $install to $fs.
  * define the DEPENDS variable for specified package; you may omit this
    definition, then it will mean the following:
    * for the *main package*: it don't depends on any package;
    * for the *split packages*: it depends exclusively on *main package*.
    Note, receipt is the shell script with all its restrictions: there's no
    difference if you define empty DEPENDS variable or not define it at all.
    Here's the small trick: if you really want to define empty dependency,
    put single space between the quotes: `DEPENDS=" "`.
  * define the two-in-one CAT variable for *split packages*. Variable format:

    ```
    CAT="category|addition"
    ```

    Where `category` is just the choosed category for the specified *split
    package*. And `addition` you will find in the brackets at the end of
    short description of the specified *split package*. You may omit this
    definition for the "dev" packages. In this case it will be implicitly
    defined as:

    ```
    CAT="development|development files"
    ```
  * define some other variables, like COOKOPTS.


Long descriptions
-----------------

You may provide `description.txt` for the *main package* and/or
`description.package-name.txt` for any of the *split package*.


`post_install()` and friends
----------------------------

You may define one of the following functions:

  * `pre_install()`;
  * `post_install()`;
  * `pre_remove()`;
  * `post_remove()`.

These functions may be defined for every of *main* or *split package*, so
you need to extend function name with underscore (`_`) and the package name.
Like this for `cookutils` package:

    post_install_cookutils()

Attention! You should know that some characters that are valid in package names
are not allowed in function names. Please, substitute each symbol that not
belongs to the intervals `A-Z, a-z, 0-9` by yet another underscore (`_`).
Example for `coreutils-disk`:

    post_install_coreutils_disk()

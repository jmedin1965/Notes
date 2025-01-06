# Available Packages not showing any packages
Created Tuesday 03 September 2024

REF" <https://www.reddit.com/r/PFSENSE/comments/11wzcxc/available_packages_not_showing_any_packages/>

Here's the fix:
ssh to pfsense as admin, enter shell.

type 'certctl rehash' to refresh the certs.

Then, In gui, go back to package manager, it should now properly refresh the list of available packages.

If you still have problems, you can try the following:
pkg-static update -f
pkg-static install -fy pkg pfSense-repo pfSense-upgrade



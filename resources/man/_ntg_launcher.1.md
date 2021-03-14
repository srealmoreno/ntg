% ntg(1) _ntg_launcher User Manuals
% Srealmoreno - Salvador Real
% October 30, 2020

# NAME

**_ntg_launcher** - Launcher for ntg

# SYNOPSIS

**_ntg_launcher** {NetguiProject}

# DESCRIPTION

Launcher for ntg.  
This is a plugin to improve the user experience when using the ntg tool avoiding the use of the terminal.  
If no arguments are passed it will open a shell session to convert projects quickly.  
If an argument (netgui project) is passed, it will try to convert the (complete) project and save it in the same folder where the project to be converted is located.  
If more arguments are passed they will be ignored.  
With this script you will be able to convert the projects from the user interface, by right clicking -> open with ntg  
If the prompt (>>) turns green it will indicate that there was an error.  
If it turns green, it will indicate that the operation was successful.  
It also waits for a timer or user action when exiting. This script saves the history in its own file '~/.ntg_history'

# SEE ALSO

ntg(1)

# BUGS

Reports bugs in <https://github.com/srealmoreno/ntg/issues>

# WIKI

<https://github.com/srealmoreno/ntg/wiki>

# LICENCE

Copyright (C) 2020 Salvador Real.

This program is free software: you can redistribute it and/or modify it under the terms of the GNU General Public License as published by the Free Software Foundation, either version 3 of the License, or (at your option) any later version.

This program is distributed in the hope that it will be useful, but WITHOUT ANY WARRANTY; without even the implied warranty of MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE. See the GNU General Public License for more details.

You should have received a copy of the GNU General Public License along with this program. If not, see <http://www.gnu.org/licenses/>.

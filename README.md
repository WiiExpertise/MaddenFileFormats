
# Madden File Formats
This repository serves to include documentation detailing various file formats used by the Frostbite-era Madden NFL games (Madden NFL 18-present). Some file formats may be found in other games as well.

## File Categories
There are two main categories of files: engine files and save files.
### Engine files
Engine files have two sub-categories: real and virtual. Real files are found in the game directory as part of the real filesystem, while virtual files are found within the virtual filesystem that is built using the real files.
 
While the real file types used tend to differ game to game, in general all games have the same virtual file types.
#### Real files
- TOC
- SB
- CAS
- CAT
#### Virtual files
- EBX
- RES
- CHUNK
- Legacy files

### Save files
Save files are file formats that are not stored as part of the downloaded game. These are generally stored in a separate folder and contain user save data.
#### Examples
- Roster files
- Franchise save files
- Draft class files

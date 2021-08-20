# UnrealLevelGenerator
Unreal Blueprint Room Based Level Generator

This is my Portfolio/Teachable Blueprints based Unreal Engine Project for randomly generated levels.

This method uses Room Blueprints which contain any number of Doors, which are used to connect Rooms.

Most of the functionaly is contained within BP_LevelGenerator's Build Rooms (which calls BuildRoom and BuildBranchRooms), and BP_Room's Construction Script.

To recreate a similar project, these two Blueprints are needed, as well as the BP_Door, and some Blueprints based off BP_Room.

BP_Door:
- This Blueprint is meant to be a connection from BP_Room to BP_Room. This is achieved by adding a Scene Object called AttachPoint. This AttachPoint Scene Object is where two BP_Doors (and therefore, their BP_Rooms) are attached, and so should be placed on the outside of the door, facing outward. The position and facing is used in BP_Room's Construction Script to determine where the neighboor BP_Room is located and oriented.

BP_Room:
- Blueprints parented by BP_Room have some Floor piece Static Mesh Components and Child Actor Components (with Child Actor Class set to BP_Door). The Static Mesh Components are iterated through when a BP_Room is being Constructed, to see if any current BP_Room's overlap. The Construction Script is where the placement of the newly spawned BP_Room is determined. The Spawn accepts a Parent/Neighboor Door, and if valid, it uses the AttachPoint Transform and the Randomly selected Door's AttachPoint Transform to Rotate and Locate this BP_Room. It then checks for overlap, and Destroys itself if overlap(s) exist.

BP_LevelGenerator:
- This BP starts with three Room Lists, and two Instance editable Inputs. The StartRooms and EndRooms Lists contain BP_Room parented BPs that include at least one BP_Door. They are spawned at the start and end of BuildRooms. The Rooms List contains BP_Room parented BPs that include at least two BP_Doors. The MainRoomCount Integer is the number of rooms between the Start Room and End Room. The MainBranchRoomCount Integer is the max number of rooms a branch can contain, after the Main Branch is completed.
- BuildRooms starts by building a room randomly chosen from the StartRooms List, and Parent Room is left empty (which causes this room to be Spanwed at 0,0,0). It then iterates through a loop, calling BuildRoom with Parent as the last room successfully added to the MainRooms List (else, the Start Room). If the new room is valid, add it to the MainRooms List, and if it is not valid, and the last entry from the MainRooms List has no more Available Doors, Destroy it and remove it from the List (so that it can attempt another direction).
- When the MainRooms List meets the MainRoomCount value, BuildRoom is called for the End Room. If it is not valid, and the last entry from the MainRooms List has no more Available Doors, Destroy it and remove it from the List (so that it can attempt another direction). If it is valid, set IsFinished to true.
- Back in the previous loop that waits for IsFinished, BuildBranchRooms is called.
- BuildBranchRooms will follow a similar process to BuildRooms, but will stop when BuildRoom is not valid, up to the MaxBranchRoomCount.

Optimizations:
- Level Generation can potentially go along many branchs while trying to make that branch fit against the previously spawned BP_Rooms, which can result in a lot of Spawned BP_Rooms that then get Destroyed (either immediately due to overlap, or if the entire Main branch doesn't fit). BP_Rooms could eventually be complex objects, in which case, creating a 'boundary' version as a placeholder would speed up generation considerably. The boundary would just be enough information to include BP_Doors and a Static Mesh Component (or simiar) to check for overlaps.

Improvements:
- BP_Room currently iterates through all children to find Static Mesh Components to overlap against previously Spawned BP_Rooms. This could be improved by adding a List to BP_Room to iterate through instead. This would be less objects to check, but also allow a simpler type of object (or just an area), and would allow other Static Mesh Components that should not be checked for overlap.

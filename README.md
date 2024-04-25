# ObjectListView (upgraded to .NET Core 8)
ObjecListView 2.9.1 (2016-05-05), ported to .NET Core 8.0!  Check out the original here: https://objectlistview.sourceforge.net/.

This was ported by loading up the 2.9.1 source in Visual Studio 2022, upgrading it to .NET Framework 4.8, then right clicking on the ObjectListView project and selecting "Upgrade".  This option uses the [.NET Upgrade Assistant](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.upgradeassistant) to do the porting for you.  In the case of ObjectListView, I only had to tweak a few minor things like resolving some references and silencing some warnings.  See commit history for details.

# Differences from ObjectListView 2.9.1

## Using ObjectListView controls
Can't do it like in the .NET Frameowrk days where you add a DLL and then edit the toolbox to add specific controls.  It appears that the .NET Core DLL produced by this project cannot be enumerated dynamically by the code that handles the toolbox in Visual Studio so it won't see any controls.  Adding the entire ObjectListView project to whatever solution you have and then adding the ObjectListView project as a reference project to whatever project you want to use ObjectListView in seems to work, as the controls appear in the toolbox.
	
	1. Copy ObjectListView folder to target solution folder
	2. Add  ObjectListView.csproj as an existing project
	3. Add ObjectListView as a project reference to your main project
	4. The ObjectListView controls will now appear in the toolbox.  Enjoy.

## Adding columns
An exception will occur when adding a column using the properties window because Visual Studio adds the column as a **System.Windows.Forms.ColumnHeader** and not a **BrightIdeasSoftware.OLVColumn**.  The column will be added but the designer will close.  Look in designer file and change the column from:

    columnHeader1 = new ColumnHeader();

to 

    columnHeader1 = new BrightIdeasSoftware.OLVColumn();

You will also have to update a few more lines that reference the type.  Once this is done, you can then reopen the designer to edit the column's properties as desired.  Adding another column will require the same rigamarole.  There may be a way to tell Visual Studio to add the right type of column from the get-go, but I don't know what that would be.

## VirtualListSize

That there is an issue with VirtualObjectListView::VirtualListSize().  It is overriding the base class to eliminate a flicker, but I could not get it working in .NET Core without throwing exceptions whenever an item is added.  I commented out the entire method.  Not sure if this has other ripple effects...
# How to Add Custom Items in ServUO Using UO Fiddler and ClassicUO

This guide explains how to create a custom item for **Ultima Online (ServUO)**, including **editing art, adding it to the game, and recompiling the server**.

---

## ⚠️ Disclaimer

These steps **worked for this user**, but **your setup may vary**. Paths, filenames, and procedures **may need slight adjustments** based on your installation of ServUO, ClassicUO, and UO Fiddler.

---

## 📌 Step 0: Finding and Customizing the Graphic HEX ID

Before creating your custom item, you need to **find an unused graphic ID** to assign your new art.

1. **Open UO Fiddler**:
   - Launch **UO Fiddler** and navigate to the **Art Tab**.

2. **Locate an Unused Art Slot**:
   - Scroll through the existing graphics.
   - Look for entries marked **"UNUSED"**.
   - Select an unused slot.

3. **Note the HEX and Decimal IDs**:
   - Upon selecting an unused slot, observe the **"Graphic"** value in the right panel.
   - This value is presented in **HEX format** (e.g., `0x18BC`).
   - The **decimal equivalent** is also displayed (e.g., `6332`).

4. **Customize Your Item Based on This ID**:
   - When creating your item script later, use the **decimal ID** (`6332` in this example) to reference your custom art.

---

## 📌 Step 1: Create Your Custom Item Art

1. **Open an Image Editor**:
   - Use **Photoshop**, **GIMP**, or **Paint.NET**.

2. **Create a New Image**:
   - **Dimensions**: `44x44 pixels` (recommended for inventory items).
   - **Color Mode**: **RGB (24-bit)**.

3. **Design Your Item**:
   - Ensure the design fits within the `44x44` pixel canvas.
   - Use a **white background** (`RGB 255,255,255`) to represent transparency.

4. **Save the Image as BMP**:
   - **Format**: **24-bit Bitmap (`.bmp`)**.
   - In **Photoshop**:
     - Go to **File > Save As**.
     - Choose **BMP**.
     - Select **24-bit** in the options.
   - In **Paint**:
     - Click **File > Save As**.
     - Choose **24-bit Bitmap (`.bmp`)**.

---

## 📌 Step 2: Import Your Art into UO Fiddler

1. **Open UO Fiddler** (if not already open).
2. Navigate to the **Art Tab**.
3. **Import Your BMP File**:
   - Select the **unused slot** identified earlier.
   - Click the **Import** button.
   - Choose your saved `.bmp` file.
4. **Save Your Changes**:
   - Click the **Save** button at the top-right corner.
   - UO Fiddler will generate or update the `art.mul` and `artidx.mul` files.

---

## 📌 Step 3: Update ClassicUO with the New Art Files

1. **Locate UO Fiddler's Output Directory**:
   - By default, UO Fiddler saves files in:
     ```
     %LOCALAPPDATA%\UOFiddler\
     ```
   - Access this path via File Explorer to find the modified `art.mul` and `artidx.mul` files.

2. **Identify Your ClassicUO Directory**:
   - Open **ClassicUO**.
   - Navigate to **Settings > Client**.
   - Note the **UO Directory Path** displayed.

3. **Replace Art Files in ClassicUO's Directory**:
   - Copy the `art.mul` and `artidx.mul` files from UO Fiddler's output directory.
   - Paste them into the **UO Directory** used by ClassicUO.
   - If an `artLegacyMUL.uop` file exists in this directory, **rename it** to:
     ```
     artLegacyMUL.uop.bak
     ```
     This ensures ClassicUO utilizes the `art.mul` files instead of the `.uop` files.

---

## 📌 Step 3.5: Check and Modify TileData (If Needed)

1. **Open UO Fiddler**.
2. Navigate to the **TileData tab**.
3. **Locate Your Graphic ID**:
   - If you're adding a **land tile** (ground, floors), check the **"Land Tiles"** section.
   - If you're adding an **object** (items, furniture, decorations), check the **"Static"** section.
   - Use the **search bar** to enter the **Art ID** (decimal, e.g., `6332`).
4. **Review the Properties**:
   - Some important flags include:
     - **"Impassable"** → Prevents players from walking over it.
     - **"Surface"** → Makes the object act like a table (items can be placed on it).
     - **"Background"** → Allows objects to blend into the environment.
5. **Modify as Needed** and click **Save**.

---

## 📌 Step 4: Restart ClassicUO and Verify the New Art

1. **Close ClassicUO Completely**.
2. **Relaunch ClassicUO** and log in with a **GM account**.
3. **Test the New Art**:
   ```
   [tile ART_ID
   ```
   Replace `ART_ID` with the decimal ID noted earlier (e.g., `6332`).

---

## 📌 Step 5: Create the Custom Item Script in ServUO

1. **Navigate to ServUO's Script Directory**:
   ```
   %SERVUO_PATH%\Scripts\Items\Custom\
   ```
2. **Create a New Script File**:
   - Name it `CustomItem.cs`.

3. **Implement the Item Script**:
   ```csharp
   using System;
   using Server;
   using Server.Items;

   public class CustomItem : Item
   {
       [Constructable]
       public CustomItem() : base(6332) // Replace with your Art ID
       {
           Name = "My Custom Item";
           Weight = 1.0;
       }

       public CustomItem(Serial serial) : base(serial)
       {
       }

       public override void Serialize(GenericWriter writer)
       {
           base.Serialize(writer);
           writer.Write(0);
       }

       public override void Deserialize(GenericReader reader)
       {
           base.Deserialize(reader);
           int version = reader.ReadInt();
       }
   }
   ```

4. **Save the Script File**.

---

## 📌 Step 6: Recompile and Restart ServUO

1. **Stop the ServUO Server** if it's running.
2. **Compile the Server**:
   - Navigate to the ServUO root directory.
   - Run the compilation script:
     ```
     _winrelease.bat
     ```
3. **Restart the ServUO Server**.

---

## 📌 Step 7: Add and Test the Custom Item In-Game

1. **Log into the Game as a GM**.
2. **Add the Custom Item**:
   ```
   [add customitem
   ```
   - If this doesn't work, try:
     ```
     [add 6332
     ```

---

## 📌 Troubleshooting

| Issue | Solution |
|--------|------------|
| `[tile 6332]` does not show the item | ClassicUO may still be using `artLegacyMUL.uop`. Rename it and restart. |
| `[add customitem]` fails | Check `CustomItem.cs` for errors. Recompile ServUO. |
| Compilation fails (`CS0246: 'Item' not found`) | Ensure `using Server;` and `using Server.Items;` are at the top of the script. |
| The item appears but is invisible | Check the image format. Must be **24-bit BMP**. |

---

## 🎉 Done!

If everything worked, **your new custom item is now in the game!** 🚀  
Modify the item script for additional features, like making it usable or equippable.

---

## ❓ Need Help?

If you encounter issues, check:
- **ServUO Console Logs** for errors.
- **ClassicUO logs** (`ClassicUO/Data/files.json` might need deleting).
- **UO Fiddler** to confirm Art ID placement.

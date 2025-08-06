# myArchive

To add custom car models to your Open.MP server using the DL version (which supports objects but not custom vehicles), follow this step-by-step solution:

### 1. **Download Car Models**
   - Source models from trusted modding sites like:
     - [GTA5-Mods.com](https://www.gta5-mods.com/vehicles) (filter for GTA San Andreas)
     - [LibertyCity.ru](https://www.libertycity.ru/)
   - Ensure models are in `.dff` (mesh) and `.txd` (texture) formats compatible with GTA San Andreas.

---

### 2. **Convert Models to Objects**
Since Open.MP DL doesn't support custom vehicles, you'll convert car models into **attachable objects** (i.e., standard world objects that visually replace the vehicle). This ensures compatibility while preserving custom visuals.

#### Tools Needed:
- **ZModeler 3**: Import and prepare models
- **TXD Workshop**: Manage and edit textures
- *(Optional)* **Kam's Scripts** (for 3ds Max): If using 3ds Max for exporting `.dff`/`.txd`

---

#### Conversion Steps (Vehicle → Object):

1. **Import the Car Model**:
   - Open ZModeler 3.
   - Load the downloaded car model (typically in `.dff`, `.obj`, or `.3ds` format).
   - Make sure all parts are visible (body, wheels, accessories).

2. **Prepare the Model**:
   - **Remove extra functionality**:
     - Delete unused parts (e.g., damaged versions, LODs, engine animations).
     - Strip out any dummy helpers for wheels, doors, lights — they're unnecessary for object mode.
   - **Center the model**:
     - Position the vehicle so that its center point is at the origin `(0, 0, 0)`.
     - Align it so the wheels rest exactly on the ground plane (Z = 0).
   - **Scale the model**:
     - Use reference from original GTA SA vehicles (e.g., import an Infernus into ZModeler and match size).
     - A common mistake is models being too large or small in-game — fix this here.
   - **Merge all geometry into one object**:
     - Combine car parts into a single mesh to improve performance and compatibility.

3. **Assign Materials and Textures**:
   - Ensure materials are properly mapped.
   - If your model uses multiple textures, consider combining them into one texture sheet to simplify `.txd` handling.

4. **Export as `.dff`**:
   - In ZModeler 3:
     - File → Export → GTA SA (*.dff)
     - Make sure export settings are for “object” type (not vehicle).
   - Save as `modelname.dff` (e.g., `elegy_custom.dff`)

5. **Create `.txd` (Texture Dictionary)**:
   - Open **TXD Workshop**.
   - Create a new `.txd` file (File → New).
   - Import all textures used in the model.
     - Match texture names exactly with material references from `.dff`.
   - Save as `modelname.txd` (e.g., `elegy_custom.txd`).

6. **Test Locally (Optional)**:
   - You can load the object in SA-MP Map Editor or MTA to confirm the model displays correctly, using a dummy object ID.

---

> ✅ **Result**: You now have a `.dff` and `.txd` file representing a car model as an *object*, ready to be used with `AddSimpleModel` in Open.MP.

---

### 3. **Add Objects to Open.MP Server**
   #### Server-Side Setup:
   1. **Place Files**:
      - Put `.dff` and `.txd` files in:
        ```
        server/models/custom_cars/
        ```
   2. **Load Models in Script**:
      Use `AddSimpleModel` in your gamemode/filterscript:
      ```pawn
      public OnGameModeInit() {
          // Add custom car object (baseid: unused SA object, newid: custom ID)
          AddSimpleModel(-1, 19342, 20000, "custom_cars/infernus.dff", "custom_cars/infernus.txd");
          return 1;
      }
      ```
      - `19342`: Base ID (e.g., a lamp post not used in your server).
      - `20000`: Custom object ID (use `20000-30000` range).

---

### 4. **Attach Objects to Vehicles**
   Spawn a default vehicle (e.g., Infernus) and attach your custom model as an object:

   ```pawn
   new customInfernus; // Global variable

   public OnGameModeInit() {
       // Create default vehicle
       customInfernus = CreateVehicle(411, 0.0, 0.0, 5.0, 0.0, -1, -1, 100); // 411 = Infernus

       // Attach custom object to vehicle
       new objID = CreateObject(20000, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0); // Use custom ID 20000
       AttachObjectToVehicle(objID, customInfernus, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0); // Adjust offsets
   }
   ```

   #### **Critical: Adjust Offsets**
   - Use an in-game editor (e.g., [this filterscript](https://github.com/Open-GTO/Attach-Editor)) to fine-tune:
     ```pawn
     AttachObjectToVehicle(objID, customInfernus, offsetX, offsetY, offsetZ, rotX, rotY, rotZ);
     ```
   - Example offsets for a sedan:
     ```pawn
     AttachObjectToVehicle(objID, customInfernus, 0.0, 0.0, -0.5, 0.0, 0.0, 0.0); // Lowered Z
     ```

---

### 5. **Optimization & Fixes**
   - **Collision**: Disable collision for attached objects to prevent bugs:
     ```pawn
     SetObjectNoCollision(objID, 1);
     ```
   - **Streaming**: Use a streamer plugin (e.g., [Pawn.RakNet](https://github.com/katursis/Pawn.RakNet)) for large numbers of objects.
   - **Performance**: Limit polygons (aim for <10,000 per model) and texture size (512x512 max).

---

### 6. **Troubleshooting**
   - **Model Not Visible**:
     - Verify paths in `AddSimpleModel` match server files.
     - Check model ID isn’t conflicting.
   - **Texture Issues**:
     - Ensure `.txd` contains all textures referenced in `.dff`.
   - **Misalignment**:
     - Re-export model with ZModeler using GTA SA’s unit scale (1 unit = 1 meter).

---

This method bypasses Open.MP DL’s vehicle limitations by leveraging objects, allowing custom car models with minimal scripting.

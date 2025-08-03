# myArchive

To add custom car models to your Open.MP server using the DL version (which supports objects but not custom vehicles), follow this step-by-step solution:

### 1. **Download Car Models**
   - Source models from trusted modding sites like:
     - [GTA5-Mods.com](https://www.gta5-mods.com/vehicles) (filter for GTA San Andreas)
     - [LibertyCity.ru](https://www.libertycity.ru/)
   - Ensure models are in `.dff` (mesh) and `.txd` (texture) formats compatible with GTA San Andreas.

---

### 2. **Convert Models to Objects**
   Since Open.MP DL doesn't support custom vehicles, you'll convert car models into **attachable objects**:

   #### Tools Needed:
   - **Kam's Scripts** (for 3ds Max): Export models to `.dff`/`.txd`
   - **ZModeler 3**: Adjust model scale/position
   - **TXD Workshop**: Manage textures

   #### Conversion Steps:
   1. **Import Model**:
      - Open the downloaded car model in ZModeler 3.
      - Adjust scale to match GTA SA's vehicle size (reference existing vehicles).
   2. **Positioning**:
      - Center the model at origin `(0,0,0)` and align wheels to the ground.
      - Export as `.dff`.
   3. **Texture Handling**:
      - Use TXD Workshop to create a `.txd` file with all textures.
      - Ensure texture paths in the `.dff` match the `.txd` names.

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

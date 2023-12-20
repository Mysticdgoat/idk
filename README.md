import bpy

def retarget_animation(source_obj, target_obj):
    # Check if both objects are armatures
    if source_obj and source_obj.type == 'ARMATURE' and target_obj and target_obj.type == 'ARMATURE':
        # Ensure both objects are visible
        source_obj.hide_set(False)
        target_obj.hide_set(False)

        # Switch to Pose Mode for both armatures
        bpy.context.view_layer.objects.active = source_obj
        bpy.ops.object.mode_set(mode='POSE')

        bpy.context.view_layer.objects.active = target_obj
        bpy.ops.object.mode_set(mode='POSE')

        # Iterate through frames
        for frame in range(bpy.context.scene.frame_start, bpy.context.scene.frame_end + 1):
            bpy.context.scene.frame_set(frame)

            # Iterate through bones in the source armature
            for bone in source_obj.pose.bones:
                bone_name = bone.name
                target_bone = target_obj.pose.bones.get(bone_name)

                if target_bone:
                    # Transfer keyframes directly
                    target_bone.rotation_euler = bone.rotation_euler
                    target_bone.location = bone.location
                    target_bone.scale = bone.scale

                    # Set keyframes for the target bone
                    target_bone.keyframe_insert("rotation_euler")
                    target_bone.keyframe_insert("location")
                    target_bone.keyframe_insert("scale")

        print(f"Animation transferred successfully from '{source_obj.name}' to '{target_obj.name}'.")

    else:
        print("Both objects must be valid armatures.")

# Replace "Armature" and "GLTF_created_0" with your source and target armature names
source_armature = bpy.data.objects.get("Armature")
target_armature = bpy.data.objects.get("GLTF_created_0")

# Call the retarget_animation function
retarget_animation(source_armature, target_armature)

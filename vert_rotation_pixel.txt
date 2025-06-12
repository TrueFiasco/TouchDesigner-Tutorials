// GLSL TOP for TouchDesigner that rotates 33 points in 4D space
// Input texture contains 33 points with XYZW coordinates
// Rotation order: RWX, WRY, WRZ, then RX, RY, RZ

//uniform sampler2D sTD2DInputs[1];  // Input texture with 33 points (XYZW coordinates)
uniform vec3 rotWXYZ;           // Rotations in W space (RWX, WRY, WRZ) in radians
uniform vec3 rotXYZ;            // Rotations in 3D space (RX, RY, RZ) in radians

out vec4 fragColor;

// Helper function to create 4D rotation matrix for W-X plane
mat4 rotateWZ(float angle) {
    float c = cos(angle);
    float s = sin(angle);
    return mat4(
        1.0, 0.0, 0.0, 0.0,
        0.0, 1.0, 0.0, 0.0,
        0.0, 0.0, c, -s,
        0.0, 0.0, s, c
    );
}

// Helper function to create 4D rotation matrix for W-Y plane
mat4 rotateWY(float angle) {
    float c = cos(angle);
    float s = sin(angle);
    return mat4(
        1.0, 0.0, 0.0, 0.0,
        0.0, c, 0.0, -s,
        0.0, 0.0, 1.0, 0.0,
       0.0, s, 0.0, c
    );
}

// Helper function to create 4D rotation matrix for W-Z plane
mat4 rotateWX(float angle) {
    float c = cos(angle);
    float s = sin(angle);
    return mat4(
        c, 0.0, 0.0, -s,
        0.0, 1.0, 0.0, 0.0,
        0.0, 0.0, 1.0, 0.0,
        s, 0.0, 0.0, c
    );
}

// Helper function to create 3D rotation matrix for X axis (embedded in 4D)
mat4 rotateX(float angle) {
    float c = cos(angle);
    float s = sin(angle);
    return mat4(
        1.0, 0.0, 0.0, 0.0,
        0.0, c, -s, 0.0,
        0.0, s, c, 0.0,
        0.0, 0.0, 0.0, 1.0
    );
}

// Helper function to create 3D rotation matrix for Y axis (embedded in 4D)
mat4 rotateY(float angle) {
    float c = cos(angle);
    float s = sin(angle);
    return mat4(
        c, 0.0, s, 0.0,
        0.0, 1.0, 0.0, 0.0,
        -s, 0.0, c, 0.0,
        0.0, 0.0, 0.0, 1.0
    );
}

// Helper function to create 3D rotation matrix for Z axis (embedded in 4D)
mat4 rotateZ(float angle) {
    float c = cos(angle);
    float s = sin(angle);
    return mat4(
        c, -s, 0.0, 0.0,
        s, c, 0.0, 0.0,
        0.0, 0.0, 1.0, 0.0,
        0.0, 0.0, 0.0, 1.0
    );
}

void main() {
    // Get normalized texture coordinates
    vec2 uv = vUV.st;
    
    // Sample the input point from texture
    vec4 point = texture(sTD2DInputs[0], uv)*2.-1;
    
    // Create combined 4D rotation matrix in specified order (order dependent):

    mat4 rotationMatrix = 
        rotateZ(rotXYZ.z) * 
        rotateY(rotXYZ.y) * 
        rotateX(rotXYZ.x) * 
        rotateWZ(rotWXYZ.z) * 
        rotateWY(rotWXYZ.y) * 
        rotateWX(rotWXYZ.x);

    
    // Apply rotation to the point
    vec4 rotatedPoint = rotationMatrix * point;
    
    // Output the rotated point
    fragColor = rotatedPoint;
}
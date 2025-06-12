// pixel 4D perspective created by truefiasco.org/tesseract-tutorial.html
uniform float fov;			// Field of view in degrees. use 60
uniform vec2 resolution;	// Resolution of the output TOP. use 33
uniform float uPerspective; // use 2.3
uniform float cameraZ;		// use -30

out vec4 fragColor;
void main() {
	// Normalize UV and get pixel index
	vec2 uv = gl_FragCoord.xy / resolution;
	int index = int(uv.x * resolution);
	float texX = (float(index) + 0.5) / int(resolution);
	
	vec4 pos = texture(sTD2DInputs[0], vec2(texX, 0.5)).rgba;
	pos.rgb *= pos.a + uPerspective.x;

	// Translate point relative to camera
	vec3 camPos = vec3(0.0, 0.0, cameraZ);
	vec3 relPos = pos.rgb - camPos;

	// Convert FOV to focal length
	float f = 1.0 / tan(radians(fov) * 0.5);

	// Perform perspective divide
	vec2 projected;
	projected.x = f * relPos.x / relPos.z;
	projected.y = f * relPos.y / relPos.z;

	// Normalize projected coords to [0,1] for visualization (optional)
	vec2 normXY = projected * 0.5 + 0.5;

	// Output projected position in red and green channels
	fragColor = vec4(normXY,0.0, 1.0); // use fragColor = vec4(pos.rgb, 1.0); for a 3D perspective
}

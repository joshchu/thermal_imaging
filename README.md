# thermal_imaging

This is a prototype for enforcing temperature requirements prior to clockin for employees. There are two parts to this:
* Takes a thermal image from a FLIR camera (current examples are FLIR ONE gen 3) and looks for the maximum temperature point in the image. (Note: this is a shortcut - a real implementation would recognize the face first and test only the area of the face)
* Once the thermal image passes, we go to step 2 and recognize the employee. (Note: this example is slow and gets the face embeddings for each known employee and checks the test image/employee)


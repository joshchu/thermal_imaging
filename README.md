# thermal_imaging

This is a prototype for enforcing temperature requirements prior to clockin for employees. There are two parts to this:
* Takes a thermal image from a FLIR camera (current examples are FLIR ONE gen 3) and looks for the maximum temperature point in the image. (Note: this is a shortcut - a real implementation would recognize the face first and test only the area of the face)
* Once the thermal image passes, we go to step 2 and recognize the employee. (Note: this example is slow and gets the face embeddings for each known employee and checks the test image/employee)

## reference
FLIR thermal camera SDK: https://developer.flir.com/getting-started/ios-platform-guide-flir-one/ (Josh has a FLIR ONE Gen3)
FLIR image extractor for thermal image processing: https://github.com/nationaldronesau/FlirImageExtractor
Face recognition framework: https://github.com/ageitgey/face_recognition


## Full app

Frontend
===========================================
Swift / React Native app (Expo possible?)
	- start with a basic Expo app and see if calling native Swift library for FLIR SDK is possible. If so, makes iterating on the app much easier (Expo rocks!)
		- see https://teabreak.e-spres-oh.com/swift-in-react-native-the-ultimate-guide-part-1-modules-9bb8d054db03
		- see https://developer.flir.com/forums/topic/any-experience-using-flir-sdk-within-the-react-native-framework-or-ideas-on-it/
	- use FLIR SDK to save and upload thermal image to endpoint - see https://developer.flir.com/wp-content/uploads/2015/06/FLIR_Thermal_SDK_iOS_Migration_Guide_1_0.pdf
		- https://developer.flir.com/getting-started/ios-platform-guide-flir-one/
	- to start, backend can validate image thermal footprint
	- or faster, validate temp in the SDK via FLIRImageStatistics.getMax().value()
	- make backend call to /check_employee and check result body and login employee that was recognized
	```
	{
		"result": "PASS",
		"details": {
			"temperature_check": 98.6,
			"temperature_units": "F",
			"employee_name": "Josh Chu",
			"employee_id": 12345,
			"employe_pin": 1001
		}
	}
	```


Backend
=============================================
Python / Flask backend (chosen to integrate with face recognition package)
	- see https://programminghistorian.org/en/lessons/creating-apis-with-python-and-flask for basic flask primer
	- create a POST endpoint /check_employee
		- endpoint validates temperature in thermal image and returns 200 with body error if temp exceeds server-set threshold (~100 degrees F)
		- endpoint then checks image against known employee image embeddings. For 40-50 employees it might be fast enough to iterate and check each embedding. 
			- see https://github.com/ageitgey/face_recognition for package that bundles a HOG model by default as well as several other pre-trained neural networks 
			- For bonus and better performance, use a clustering algo similar to https://github.com/ageitgey/face_recognition/blob/master/examples/face_recognition_knn.py



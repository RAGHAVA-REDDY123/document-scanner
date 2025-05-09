# document-scanner
This project implements a simple Document Scanner using basic computer vision techniques with OpenCV. The scanner can detect a document in an image, straighten it, and produce a clear, top-down "scanned" version of the document. This mimics the functionality of popular mobile scanning apps. The project is ideal for learning key image processing techniques such as edge detection, contour finding, perspective transformation, and warping.
the document-scanner pipeline consists of following steps

1.CONVERT THE IMAGE FROM BGR TO RGB FORMAT 
<br>
2.CONVERT THE RGB IMAGE INTO GRAY SCALE IMAGE
<br>
3.APPLY GUASSIAN BLUR
<br>
4.APPLY CANNY EDGE DETECTION
<br>
5.APPLY DILATION
<br>
6.FIND ALL THE CONTOURS IN THE IMAGE
<br>
7.FIND THE BIGGEST CONTOUR
<br>
8.REORDER THE DETECTED POINTS 
<br>
9.WARP THE IMAGE
<br>
10.FINALLY PRINT THE SCANNED IMAGE
<br>


🧭 Document Scanner Pipeline – Step-by-Step Description
1. Convert the Image from BGR to RGB Format
OpenCV reads images in BGR format by default. To work with libraries or visualization tools that expect RGB format (like matplotlib), or just for consistency, we first convert the image from BGR to RGB.

img_rgb = cv2.cvtColor(img, cv2.COLOR_BGR2RGB)

2. Convert the RGB Image into Grayscale
Grayscale images are easier and faster to process, especially for tasks like edge detection. This step removes color information, retaining only intensity.

img_gray = cv2.cvtColor(img_rgb, cv2.COLOR_RGB2GRAY)

3. Apply Gaussian Blur
Gaussian Blur is used to reduce image noise and detail. It smoothens the image to help in better edge detection by removing unnecessary high-frequency components.

img_blur = cv2.GaussianBlur(img_gray, (5, 5), 1)

4. Apply Canny Edge Detection
Canny is a multi-stage edge detection algorithm that helps detect edges in the image. It highlights the borders of the document and removes unwanted background edges.

img_canny = cv2.Canny(img_blur, 100, 200)

5. Apply Dilation
Dilation enlarges the edges detected by Canny to make them more prominent and continuous. This is helpful for better contour detection.

kernel = np.ones((5, 5))
img_dilated = cv2.dilate(img_canny, kernel, iterations=2)

6. Find All the Contours in the Image
Contours are simply the curves joining continuous points along a boundary. In this step, all external contours are detected from the dilated image.

contours, hierarchy = cv2.findContours(img_dilated, cv2.RETR_EXTERNAL, cv2.CHAIN_APPROX_SIMPLE)

7. Find the Biggest Contour
Among all detected contours, the one that represents the document is generally the largest quadrilateral. We find this by checking the contour with the biggest area and 4 corner points.
        
8. Reorder the Detected Points
To warp the image properly, we need the four corner points of the document in a consistent order: top-left, top-right, bottom-left, bottom-right. This step arranges the corner points accordingly.

# reorder function helps align points correctly before warping
ordered_points = reorder(biggest)

9. Warp the Image
Using the ordered points, we perform a perspective transform to obtain a top-down view of the document. This makes the document appear as if scanned from a flatbed scanner.

matrix = cv2.getPerspectiveTransform(ordered_points, desired_points)
img_warped = cv2.warpPerspective(img, matrix, (width, height))

10. Finally Print the Scanned Image
The final result is displayed or saved. This image is a clean, cropped, and corrected version of the original image that focuses solely on the document.

cv2.imshow("Scanned Document", img_warped)
<br>
cv2.imwrite("scanned_output.jpg", img_warped)

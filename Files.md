# Files

**ImageType**

IMAGE_TYPE_NONE \
IMAGE_TYPE_PNG \
IMAGE_TYPE_JPEG \
IMAGE_TYPE_GIF \
IMAGE_TYPE_BMP

---

**files_image_type_to_string ()** matches the **ImageType** with its correct string representation

``` python
files_image_type_to_string (
	ImageType			# the image type to be matched
) -> c_char_p
```

**Returns** a constant string reference to the **ImageType** name

---

**files_image_get_type ()** opens the file and checks its magic bytes to get the file's image type

``` python
files_image_get_type (
	c_char_p			# the filename to be opened
) -> ImageType
```

**Returns** the matching **ImageType** based on the file's magic bytes

---

**files_image_get_type_by_extension ()** matches the provided file extension with its correct type

``` python
files_image_get_type_by_extension (
	c_char_p			# the string value representing a file extension
) -> ImageType
```

**Returns** the correct image type based on the filename's extension

---

**files_image_extension_is_jpeg ()** checks if the provided string value matches the **jpg/jpeg** extension

``` python
files_image_extension_is_jpeg (
	c_char_p			# the string value representing a file extension
) -> c_bool
```

**Returns** true if the filename's extension is jpg or jpeg

---

**files_image_extension_is_png ()** checks if the provided string value matches the **png** extension

``` python
files_image_extension_is_png (
	c_char_p			# the string value representing a file extension
) -> c_bool
```

**Returns** true if the filename's extension is png

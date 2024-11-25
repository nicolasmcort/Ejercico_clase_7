# Ejercico_clase_7

Este ejercicio en Python define clases para representar puntos, rectángulos y cuadrados, permitiendo la creación de estas figuras de diferentes formas (por esquina, centro o dos esquinas opuestas). Incluye métodos para calcular área, perímetro y verificar si un punto o línea interfieren con la figura.

``` python
class Point:
    def __init__(self, x: float, y: float):
        self.x = x
        self.y = y

class Rectangle:
    def __init__(self, method: int, *args):
        if method == 1:  # Bottom-left corner + width and height
            self.bottom_left, self.width, self.height = args
            self.center = Point(self.bottom_left.x + self.width / 2, self.bottom_left.y + self.height / 2)
            self.upper_right = Point(self.bottom_left.x + self.width, self.bottom_left.y + self.height)
        elif method == 2:  # Center + width and height
            self.center, self.width, self.height = args
            self.bottom_left = Point(self.center.x - self.width / 2, self.center.y - self.height / 2)
            self.upper_right = Point(self.center.x + self.width / 2, self.center.y + self.height / 2)
        elif method == 3:  # Two opposite corners (bottom-left and upper-right)
            self.bottom_left, self.upper_right = args
            self.width, self.height = self.upper_right.x - self.bottom_left.x, self.upper_right.y - self.bottom_left.y
            self.center = Point(self.bottom_left.x + self.width / 2, self.bottom_left.y + self.height / 2)

        # Common calculations for all methods
        self.upper_left = Point(self.bottom_left.x, self.upper_right.y)
        self.bottom_right = Point(self.upper_right.x, self.bottom_left.y)
        self.corners = [self.bottom_left, self.bottom_right, self.upper_left, self.upper_right]

    def compute_area(self) -> float:
        return self.width * self.height

    def compute_perimeter(self) -> float:
        return 2 * (self.width + self.height)

    def compute_interference_point(self, point: Point) -> bool:
        # Check if the point is inside the rectangle
        return (self.bottom_left.x <= point.x <= self.upper_right.x) and (self.bottom_left.y <= point.y <= self.upper_right.y)

    def compute_interference_line(self, line_start: Point, line_end: Point) -> bool:
        for corner in self.corners:
            # Calculate the difference in line coordinates
            dx = line_end.x - line_start.x 
            dy = line_end.y - line_start.y 

            # Check if the line is a point, then verify if that point is inside the rectangle
            if dx == 0 and dy == 0:
                return self.compute_interference_point(Point(line_start.x, line_start.y))
            
            # Calculate the value of t that represents the projection of the 'corner' point on the line   
            t = ((corner.x - line_start.x) * dx + (corner.y - line_start.y) * dy) / (dx**2 + dy**2)
            t = max(0, min(1, t)) # Parameter t should be within range [0, 1]

            # Calculete the projected point and check if it is within the rectangle
            closet_point = Point(line_start.x + t * dx, line_start.y + t * dy)
            if rectangle.compute_interference_point(closet_point):
                return True
            
        return False
            
class Square(Rectangle):
    def __init__(self, method: int, *args):
        if method == 1:  # Bottom-left corner + side length
            super().__init__(1, args[0], args[1], args[1])  
        elif method == 2:  # Center + side length
            super().__init__(2, args[0], args[1], args[1])
        elif method == 3:  # Two opposite corners (square)
            super().__init__(3, args[0], args[1])

```

### Ejemplo de uso
```python
# Method 1: Bottom-left corner + width and height
bottom_left = Point(0, 0)
rectangle = Rectangle(1, bottom_left, 4, 2)
print(f"Area (first rectangle): {rectangle.compute_area()}")  
print(f"Perimeter (first rectangle): {rectangle.compute_perimeter()}")  

# Method 2: Center + width and height
center = Point(2, 2)
rectangle2 = Rectangle(2, center, 4, 3)
print(f"\nArea (second rectangle): {rectangle2.compute_area()}")  
print(f"Perimeter (second rectangle): {rectangle2.compute_perimeter()}")  

# Method 3: Two opposite corners
bottom_left2 = Point(0, 0)
upper_right = Point(4, 3)
rectangle3 = Rectangle(3, bottom_left2, upper_right)
print(f"\nArea (third rectangle): {rectangle3.compute_area()}")  
print(f"Perimeter (third rectangle): {rectangle3.compute_perimeter()}")  

# Square using Method 1
bottom_left3 = Point(2, 2)
square = Square(1, bottom_left3, 3)
print(f"\nArea (square): {square.compute_area()}")  
print(f"Perimeter (square): {square.compute_perimeter()}")  

# Testing interference points
test_point1 = Point(2, 1)
print(f"\nFirst point inside rectangle? {rectangle.compute_interference_point(test_point1)}")  
test_point2 = Point(5, 3)
print(f"Second point inside rectangle? {rectangle.compute_interference_point(test_point2)}")  

# Testing interference lines
line1 = [Point(3.5, 2.5), Point(5, 2)]
print(f"\nFirst line intersects rectangle? {rectangle.compute_interference_line(line_start=line1[0], line_end=line1[1])}") 
line2 = [Point(1, 1), Point(5, 3)]
print(f"Second line intersects rectangle? {rectangle.compute_interference_line(line_start=line2[0], line_end=line2[1])}") 
```

### Output
Area (first rectangle): 8
Perimeter (first rectangle): 12

Area (second rectangle): 12
Perimeter (second rectangle): 14

Area (third rectangle): 12
Perimeter (third rectangle): 14

Area (square): 9
Perimeter (square): 12

First point inside rectangle? True
Second point inside rectangle? False

First line intersects rectangle? False
Second line intersects rectangle? True

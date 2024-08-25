# ALT-AZ Coordinates for Stellar Objects when Axes are Tilted or Rotated

In astronomy, celestial coordinates such as Right Ascension (RA) and Declination (DEC) are vital for locating objects in the sky. However, for practical observation from a specific location on Earth, these coordinates must be transformed into Altitude (ALT) and Azimuth (AZ), which are relative to the observer's position.

## Part I - Converting Right Ascension and Declination to Altitude and Azimuth

This section describes the algorithm to convert RA and DEC into ALT and AZ, incorporating essential concepts like Julian Date, Julian Time, and Greenwich Mean Sidereal Time (GMST).

### 1. Key Concepts

- **Julian Date (JD)**: A continuous count of days since January 1, 4713 BC. It is used in astronomy to calculate time intervals. The Julian Date at a given Universal Time (UT) can be calculated using the formula:

  $$JD = 367 \times \text{Year} - \left\lfloor \frac{7 \times (\text{Year} + \left\lfloor \frac{\text{Month} + 9}{12} \right\rfloor)}{4} \right\rfloor + \left\lfloor \frac{275 \times \text{Month}}{9} \right\rfloor + \text{Day} + 1721013.5 + \frac{\text{UT}}{24}$$

  where `Year`, `Month`, and `Day` are calendar date components, and `UT` is the time in Universal Time.

- **Julian Time (JT)**: The fractional part of the day in Julian Date. For example, 12:00 UT is represented as $0.5$ in Julian Time.

- **Greenwich Mean Sidereal Time (GMST)**: Represents the rotation of the Earth relative to the fixed stars. It is crucial for converting RA to Hour Angle (H). GMST can be calculated from the Julian Date using:

  $$GMST = 18.697374558 + 24.06570982441908 \times (JD - 2451545.0)$$

  where $2451545.0$ is the Julian Date for the epoch J2000.0.

### 2. Algorithm Overview

#### Input Values:

- **Right Ascension ($\alpha$)**: The angular distance of a point eastward along the celestial equator.
- **Declination ($\delta$)**: The angular distance of a point north or south of the celestial equator.
- **Observer's Latitude ($\phi$)**: The observer's location on Earth.
- **Observer's Longitude ($\lambda$)**: The observer's longitudinal position on Earth.
- **Universal Time (UT)**: The current time in Universal Time (UTC).

#### Steps:

1. **Calculate Julian Date (JD) and Julian Time (JT)**:
   - Compute JD using the provided formula.
   - Extract the fractional part to determine JT.

2. **Calculate Greenwich Mean Sidereal Time (GMST)**:
   - Use JD to calculate GMST.

3. **Convert GMST to Local Sidereal Time (LST)**:

   $$LST = GMST + \frac{\lambda}{15}$$
   
   - Ensure LST is within $0^\circ$ to $360^\circ$.

5. **Calculate Hour Angle (H)**:

   $$H = LST - \alpha$$
   
   - The hour angle $H$ represents the time since the celestial object last crossed the observer's meridian.

7. **Convert to Altitude and Azimuth**:
   - **Altitude ($A$)**:
     
     $$A = \arcsin(\sin(\delta) \cdot \sin(\phi) + \cos(\delta) \cdot \cos(\phi) \cdot \cos(H))$$
     
     The altitude $A$ is the angle between the object and the observer's horizon.

   - **Azimuth ($Az$)**:
     
     $$Az = \arctan2(\sin(H), \cos(H) \cdot \sin(\phi) - \tan(\delta) \cdot \cos(\phi))$$
     
     The azimuth $Az$ is the angle from the north point of the horizon to the point directly below the object.

8. **Correct the Azimuth**:
   - Ensure the azimuth is within $0^\circ$ to $360^\circ$.
   - If negative, add $360^\circ$.

#### Output:

The algorithm outputs the Altitude ($A$) and Azimuth ($Az$) coordinates, which can be used for observational purposes.

### 3. Implementation Considerations

When implementing this algorithm, special care must be taken in handling time units and angles. Conversion between degrees and radians is necessary for trigonometric functions, and time-related calculations must account for different time zones and the distinction between UT and sidereal time.

This algorithm is essential for astronomers and hobbyists, enabling precise positioning of telescopes and other observational equipment to study celestial objects from any location on Earth.

## Part II - Coordinate Transformation Algorithm for Altitude-Azimuth Systems

This algorithm compensates for the tilts and rotations of the coordinate axes when using the Altitude (ALT) and Azimuth (AZ) coordinates in a celestial tracking system. The process is outlined as follows.

For more details, refer <a href="Part II - ALTAZ Coordinates for Tilted Axes/Concept.pdf">this</a> original hand-written document.

### 1. Initial Setup
1. **Getting Inputs**
   - RA and DEC values of a celestial object are converted to ALT and AZ values using the previos algorithm. These are the coordinates that need to be converted to new ALT-AZ system compensating for tilts and rotations of the axes.

   - Distance to the celestial object, denoted by $r$, is assumed to be on the inner surface of a sphere.

2. **Conversion to Radians:**
   - The AZ (Azimuth) and ALT (Altitude) values are converted from degrees to radians.

### 2. Algorithm Overview

![Axes_1](https://github.com/user-attachments/assets/d02729aa-f318-4ebf-9731-48cff884f7d3)

1. **Coordinate System Correction:**
   - Adjustments are made to the azimuth ($\theta$) and altitude ($\phi$) to align with the specific Cartesian coordinate system being used:
   
     $$\theta = 2\pi - \text{az}$$
     
     $$\phi = \frac{\pi}{2} - \text{alt}$$
     
     $$\rho = r$$

2. **Cartesian Coordinates Conversion:**
   - The ALT-AZ coordinates are converted into Cartesian coordinates for easier manipulation:
     
     $$x = \rho \cdot \sin(\phi) \cdot \cos(\theta)$$
     
     $$y = \rho \cdot \sin(\phi) \cdot \sin(\theta)$$
     
     $$z = \rho \cdot \cos(\phi)$$
     
   - These are represented as a column vector $X$:
     
     $$X = [ x , y , z ]^T$$

![Axes_2](https://github.com/user-attachments/assets/43848c74-ca98-4376-bceb-a19a332817b7)

3. **Tilt and Rotation Compensation:**
   - Define the tilt and rotation angles ($\alpha$, $\beta$, $\gamma$) in radians, which represent the tilt and rotation around the x, y, and z axes, respectively.

   - Calculate the coefficients of the new coordinate system, which are used to define the transformation matrix $A$:

     $$x' = [ \cos(\alpha) , -\sin(\alpha) , \sin(\beta) ]^T$$
     
     $$y' = [ \sin(\alpha) , \cos(\alpha) , \sin(\gamma) ]^T$$
     
     $$z' = [ p , q , r ]^T$$

     where:
     
     $$r = \frac{1}{\sqrt{(\sin^2(\gamma) \cdot \sin^2(\alpha)) + (\sin^2(\beta) \cdot \cos^2(\alpha)) + (\sin^2(\alpha) \cdot \sin^2(\beta)) + (\sin^2(\gamma) \cdot \cos^2(\alpha)) + 1}}$$
  
     
     $$p = r \cdot (\sin(\gamma) \cdot \sin(\alpha) - \sin(\beta) \cdot \cos(\alpha))$$
     
     
     $$q = r \cdot (\sin(\alpha) \cdot \sin(\beta) + \sin(\gamma) \cdot \cos(\alpha))$$

4. **Transformation Matrix Application:**
   - Construct the transformation matrix $A$ using the new basis vectors:
     
     $$A = [ x', y', z' ]^T$$

   - Apply the transformation matrix to the original Cartesian coordinates $X$ to obtain the new coordinates:
     
     $$X_{\text{new}} = A^{-1}X$$
     
   - Extract the new coordinates $x'\_{\text{new}}$, $y'\_{\text{new}}$, and $z'\_{\text{new}}$.

5. **Conversion Back to ALT-AZ:**
   - Convert the transformed Cartesian coordinates back into ALT-AZ:
     
     $$\theta' = \arctan2(y'\_{\text{new}}, x'\_{\text{new}})$$
     
     $$\phi' = \arctan2(x'\_{\text{new}}, z'\_{\text{new}} \cdot \cos(\theta'))$$

   - Adjust for correct ranges to get the final azimuth and altitude:

     $$\text{if } \theta' < 0 \text{, then } \theta' = \theta' + 2\pi$$
     
     $$\text{if } \phi' < 0 \text{, then } \phi' = \phi' + \pi$$
     
     $$\text{azimuth} = 2\pi - \theta'$$
     
     $$\text{altitude} = \frac{\pi}{2} - \phi'$$

![Axes_3](https://github.com/user-attachments/assets/a548e014-d9f7-4af3-94c6-29fb07ae844f)

### 3. Implementation Considerations

This algorithm is essential for accurately compensating for physical misalignments in telescope mounts, ensuring that the celestial object is correctly tracked in the sky.

The directions for the angles $\alpha$, $\beta$, and $\gamma$ should be taken carefully.


# Bypass_Lambert_W
import cmath
import math


def vertex_anchored_solver(b, m, branch="right"):
    """Solves the transcendental equation: b^x - m*x = 0

    Bypasses the Lambert W function using Dev Shah's 6-step method.
    Supports both real and complex domains automatically.
    """
    # Step 1 & 2: Formulate Gap Function and Locate the Anchor Point (Vertex)
    # Handle natural log for complex space if m/ln(b) is negative
    ln_b = math.log(b)

    # Calculate horizontal coordinate of the vertex (x0)
    # Using cmath to naturally handle negative arguments with complex numbers
    x0 = cmath.log(m / ln_b) / ln_b

    # Calculate vertical depth of the vertex valley (y0)
    y0 = b**x0 - m * x0

    # Step 3: Compute the Curvature Coefficient (Second Derivative)
    f_double_prime = (b**x0) * (ln_b**2)

    # Step 4: Execute the Quadratic Shortcut
    # Calculate the radical part of the parabola intersection
    radical = cmath.sqrt((-2 * y0) / f_double_prime)

    # Select branch (plus for right-side/W0 root, minus for left-side/W-1 root)
    if branch == "right":
        x_quad = x0 + radical
    elif branch == "left":
        x_quad = x0 - radical
    else:
        raise ValueError("Branch must be 'right' or 'left'")

    # Step 5: Apply the Derivative Correction Step ("The Snap")
    # Calculate Height Error (H)
    H = (b**x_quad) - (m * x_quad)

    # Calculate Steepness Slope (S)
    S = (b**x_quad) * ln_b - m

    # Calculate Correction Step (Delta x)
    delta_x = -H / S

    # Step 6: Teleport to the Final Root
    x_final = x_quad + delta_x

    # Clean up output formatting for readability
    return clean_complex_output(x_final)


def clean_complex_output(val):
    """Auxiliary function to clean up floating-point decimals and imaginary parts."""
    real = round(val.real, 6)
    imag = round(val.imag, 6)

    # If the imaginary part is practically zero, return it as a pure float
    if abs(imag) < 1e-5:
        return real
    return complex(real, imag)


# =====================================================================
# LIVE TESTING DEV'S PIPELINE
# =====================================================================
if __name__ == "__main__":
    print("--- Testing Dev Shah's Algebraic Bypass Pipeline ---\n")

    # Test Case 1: Real Roots (e^x - 3x = 0, base e, m = 3)
    # Expected analytical solution is approx 1.512134
    root_real = vertex_anchored_solver(b=math.e, m=3, branch="right")
    print(f"Test 1: e^x - 3x = 0 (Real Domain)")
    print(f"-> Dev's Solved Root: {root_real}\n")

    # Test Case 2: Complex Roots (e^x - 1x = 0, base e, m = 1)
    # The real curve never touches zero, forcing complex evaluation
    root_complex = vertex_anchored_solver(b=math.e, m=1, branch="right")
    print(f"Test 2: e^x - 1x = 0 (Complex Domain)")
    print(f"-> Dev's Solved Root: {root_complex}\n")
    

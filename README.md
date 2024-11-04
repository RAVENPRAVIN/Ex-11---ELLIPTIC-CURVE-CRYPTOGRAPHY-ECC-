# Ex-11---ELLIPTIC-CURVE-CRYPTOGRAPHY-ECC-

# AIM:
To perform key exchange using the Elliptic Curve Cryptography (ECC) method.

# ALGORITHM:
Choose a large prime number p and an elliptic curve defined by the equation y^2 = x”3 + ax +b mod p along with a base point G on the curve. Alice and Bob choose private keys. Compute public keys: pubIic_key = private_key * G (point multiplication). Exchange public keys. Compute the shared secret: shared_secret = private_key * pubIic_key received.


# PROGRAM:
~~~
#include <stdio.h>

// A simple structure to represent points on the elliptic curve
typedef struct {
    long long int x, y;
} Point;

// Function to compute modular inverse (using Extended Euclidean Algorithm)
long long int modInverse(long long int a, long long int m) {
    long long int m0 = m, t, q;
    long long int x0 = 0, x1 = 1;
    
    if (m == 1) return 0;
    
    while (a > 1) {
        q = a / m;
        t = m;
        m = a % m;
        a = t;
        t = x0;
        x0 = x1 - q * x0;
        x1 = t;

        if (x1 < 0) x1 += m0;
    }
    
    return x1;
}

// Function to perform point addition on elliptic curves
Point pointAddition(Point P, Point Q, long long int a, long long int p) {
    Point R;
    long long int lambda;

    // Check if P == Q (Point doubling)
    if (P.x == Q.x && P.y == Q.y) {
        lambda = (3 * P.x * P.x + a) * modInverse(2 * P.y, p) % p;
    } else {
        // Point addition
        lambda = (Q.y - P.y) * modInverse(Q.x - P.x, p) % p;
    }

    // Calculate resulting point
    R.x = (lambda * lambda - P.x - Q.x) % p;
    R.y = (lambda * (P.x - R.x) - P.y) % p;

    // Ensure values are positive
    R.x = (R.x + p) % p;
    R.y = (R.y + p) % p;
    
    return R;
}

// Function to perform scalar multiplication (Elliptic Curve Point Multiplication)
Point scalarMultiplication(Point P, long long int k, long long int a, long long int p) {
    Point result = {0, 0}; // Initialize result to the point at infinity
    Point addend = P; // Start with the base point

    while (k > 0) {
        if (k % 2 == 1) {
            result = pointAddition(result, addend, a, p);
        }
        addend = pointAddition(addend, addend, a, p); // Double the point
        k /= 2;
    }

    return result;
}

int main() {
    long long int p, a, b, privateA, privateB;
    Point G, publicA, publicB, sharedSecretA, sharedSecretB;

    // Step 1: Input parameters of the elliptic curve
    printf("Enter the prime number (p): ");
    scanf("%lld", &p);
    printf("Enter the curve parameters (a and b) for equation y^2 = x^3 + ax + b: ");
    scanf("%lld %lld", &a, &b);
    printf("Enter the base point G (x and y): ");
    scanf("%lld %lld", &G.x, &G.y);

    // Step 2: Alice and Bob input private keys
    printf("Enter Alice's private key: ");
    scanf("%lld", &privateA);
    printf("Enter Bob's private key: ");
    scanf("%lld", &privateB);

    // Step 3: Compute public keys (Elliptic Curve Point Multiplication)
    publicA = scalarMultiplication(G, privateA, a, p); // Alice's public key
    publicB = scalarMultiplication(G, privateB, a, p); // Bob's public key

    printf("Alice's public key: (%lld, %lld)\n", publicA.x, publicA.y);
    printf("Bob's public key: (%lld, %lld)\n", publicB.x, publicB.y);

    // Step 4: Compute shared secrets (Elliptic Curve Point Multiplication)
    sharedSecretA = scalarMultiplication(publicB, privateA, a, p); // Alice's shared secret
    sharedSecretB = scalarMultiplication(publicA, privateB, a, p); // Bob's shared secret

    // Step 5: Display shared secret
    printf("Shared secret computed by Alice: (%lld, %lld)\n", sharedSecretA.x, sharedSecretA.y);
    printf("Shared secret computed by Bob: (%lld, %lld)\n", sharedSecretB.x, sharedSecretB.y);

    if (sharedSecretA.x == sharedSecretB.x && sharedSecretA.y == sharedSecretB.y) {
        printf("Key exchange successful. Both shared secrets match!\n");
    } else {
        printf("Key exchange failed. Shared secrets do not match.\n");
    }

    return 0;
}

~~~
# OUTPUT:
![alt text](Output.png)
# RESULT:
The program for Elliptic Curve Cryptography (ECC) was executed successfully, and both Alice and Bob computed the same shared secret.
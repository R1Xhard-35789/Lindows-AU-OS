# **The Rolling Release Tenant**

The Lindows AU project is committed to providing a **stable, secure, and current** operating system while adhering to our "Right to Repair" philosophy. Our support model is designed around the lifecycle of the underlying Ubuntu base and the specific desktop environment we use.

---

## **I. The 5-Year / 3-Year Support Reality**

We utilize **XFCE**, which is an official Ubuntu community flavor. This choice dictates our support strategy:

| Component | Support Duration | Managed By | Lindows AU Stance |
| :---- | :---- | :---- | :---- |
| **Core System & Kernel** | **5 Years** | Canonical (Ubuntu) | Fully trusted; provides core security patches. |
| **Desktop Environment (XFCE)** | **3 Years** | Xubuntu Community Team | We respect this shorter window for major features and desktop application support. |

The crucial takeaway is that while the **desktop environment** only has 3 years of dedicated support, the **security core remains patched by Canonical for the full 5 years** of the LTS cycle. Your system will not become insecure after three years.

---

## **II. The Lindows AU Commitment: The Yearly Build**

To provide the highest quality assurance, minimize potential bugs from aging desktop components, and proactively address hardware changes, Lindows AU operates on a **Yearly Rebuild Schedule**.

### **Release Cycle**

We commit to releasing a new, major version of the Lindows AU ISO **once per year**.

* **Current Stable Base:** Ubuntu 24.04 LTS (Initial release: April 2024).  
* **Next Planned Release (Example):** April 2025 (Lindows AU v1.1).

### **Why a Yearly Build?**

1. **Guaranteed Freshness:** This schedule ensures that Lindows AU always runs on a recent kernel and receives the latest driver updates, which is essential for ensuring recycled hardware continues to function reliably.  
2. **Mitigating the "3-Year Gap":** By releasing a new build every year, we refresh the desktop components well before the 3-year support window closes, ensuring our users always have a fully supported desktop environment.  
3. **Feasibility:** The Cloud Factory Protocol allows us to compile and test a new ISO quickly and affordably, making a frequent release cycle both responsible and sustainable.

---

## **III. The V1 Stability Guarantee**

Once the Lindows AU ISO is tested and declared **V1 Stable**, we commit to the following support policies:

* **LTS Alignment:** The Lindows AU V1 lifecycle will adhere to the **full 5-year security support window** of the underlying Ubuntu LTS base.  
* **Maintenance:** We commit to releasing maintenance updates (V1.0.1, V1.0.2, etc.) **monthly** for critical security fixes, driver updates, and minor aesthetic bug corrections.  
* **Hardware Support:** We commit to maintaining the custom package lists and configurations necessary for **legacy hardware support** (e.g., specific driver additions) throughout the V1 lifecycle.

**Deviation Policy:** We only deviate from this schedule for critical security updates or widespread hardware failure that demands an immediate, off-schedule release.

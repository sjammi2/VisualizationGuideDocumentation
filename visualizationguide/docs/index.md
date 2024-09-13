# Welcome to The Illinois Numerical Relativity Visualization Primer 


##### Eric Yu<sup>1,2</sup>, Shreyas Jammi<sup>1,2</sup>, Mit Kotak<sup>1,2</sup>, Antonios Tsokaros<sup>1,2,3</sup>, Milton Ruiz<sup>4</sup>, and Stuart L. Shapiro<sup>1,2<sup>



<sup>1</sup>*Department of Physics, University of Illinois at Urbana-Champaign, Urbana, IL 61801, USA*

<sup>2</sup>*National Center of Supercomputing Applications, University of Illinois at Urbana-Champaign, Urbana, IL 61801, USA*

<sup>3</sup>*Research Center of Astronomy and Applied Mathematics, Academy of Athens, Athens 11527, Greece*

<sup>4</sup>*Departament d’Astronomia i Astrofı́sica, Universitat de València, C/ Dr Moliner 50, 46100, Burjassot (València), Spain*


<h4 style="text-align: center;">Abstract</h4>

Over the past several decades the advent of new algorithms and new computers made possible
numerous advances in computational physics, including numerical relativity, the computational
study of Einstein’s equations of general relativity. Full 3+1-dimensional simulations of black
holes and neutron stars that could include magnetic fields and detailed microphysics has become
possible, and together with them, the need to analyze complicated large sets of data. Visualizing
such datasets is important in order to better understand the problem at hand, as well as to
efficiently communicate cutting-edge research to a larger audience. One open source software
package that has emerged as a leader in supercomputing visualization, especially in the field of
numerical relativity, is VisIt. This document describes techniques developed in the Relativity
Group at the University of Illinois at Urbana-Champaign over the past couple of decades to
visualize numerical relativity simulations using the VisIt software. This guide is meant to be
used as a resource for implementing visualizations using VisIt for numerical relativity and for
further improving the visualization efforts of similar computationally intensive studies.

Keywords: **VisIT; Visualization; Numerical Relativity**

-------------------


<h4 style="text-align: center;">Contents</h4>

1. Introduction
2. Requirements and Feasibility
    1. Installation on Windows
    2. Installation on Mac
    3. Installation on Linux
3. Interacting with VisIT
    1. Interacting with VisIT GUI
    2. Interacting with VisIT CLI
4. VisIT Elements
    1. Databases
        1. HDF5 Data
        2. VTK Files
    2. Plots and Operators
        1. Volume rendering
        2. Isosurface rendering
        3. Vector fields
        4. Streamline Integration
    3. Expressions
    4. Exporting Attributes
    5. Finalizing Images
5. Typical VisIT Workflow in Numerical Relativity
    1. Black Holes and Spin Vectors
    2. Density
        1. Isosurface
        2. Volume
    3. Magnetic Field Lines
        1. Particle Seeds
        2. Grid Seeds
    4. Fluid Velocity Arrows
6. Case Study: Magnetars
    1. Poloidal Field Lines
    2. Toroidal Field Lines
    3. Final Image and Summary
7. Case Study: Black Hole with Accration Disk
    1. Isosurface Rendering of Disk
    2. Volume Rendering of Disk
    3. Magnetic Field Lines Around the Black hole
    4. Visualizing a Relativisitic Jet
8. Case Study: Binary Neutron Stars
    1. Before Black Hole Formation
    2. After Black Hole Formation
    3. Additional Plots After Merger
9. Case Study: Binary Black Hole with Accretion Disk
    1. Magnetic Field Lines in the Disk
    2. Two Black Holes
10. Visualizing Gravitational Waves
    1. Post-Processing Implementation
    2. Three Dimensional Volume Plot
    3. Two Dimensional Contour Plot

------
- Introduction
- Requirements and Feasibility
    - Installation on Windows
    - Installation on Mac
    - Installation on Linux
- Interacting with VisIT
    - Interacting with VisIT GUI
    - Interacting with VisIT CLI
- VisIT Elements
    - Databases
        - HDF5 data
        - VTK Files
    - Plots and Operators
        - Volume rendering
        - Isosurface rendering
        - Vector fields
        - Streamline integration
    - Expressions
    - Exporting Attributes
    - Finalizing Images
- Typical VisIT Workflow in Numerical Relativity
    - Black Holes and Spin Vectors
    - Density
        - Isosurface
        - Volume
    - Magnetic Field Lines
        - Particle Seeds
        - Grid Seeds
    - Fluid Velocity Arrows
- Case Study: Magnetars
    - Poloidal Field Lines
    - Toroidal Field Lines
- Case Study: Black Hole with Accration Disk
    - Isosurface Rendering of Disk
    - Volume Rendering of Disk
    - Magnetic Field Lines Around the Black Hole
    - Visualizing a Relativisitic Jet
- Case Study: Binary Neutron Stars
    - Before Black Hole Formation
    - After Black Hole Formation
    - Additional Plots After Merger
- Case Study: Binary Black Hole with Accretion Disk
    - Magnetic Field Lines in the Disk
    - Two Black Holes
- Visualizing Gravitational Waves
    - Post-Processing Implementation
    - Three Dimensional Volume Plot
    - two Dimensional Contour Plot


<style>
  .toc-container {
    border: 1px solid #ddd;
    border-radius: 4px;
    margin: 1em 0;
    background-color: #f9f9f9;
  }
  .toc-container summary {
    font-weight: bold;
    padding: 0.5em 1em;
    background-color: #f0f0f0;
    border-bottom: 1px solid #ddd;
    cursor: pointer;
  }
  .toc-container summary:hover {
    background-color: #e0e0e0;
  }
  .toc-content {
    padding: 0.5em 1em;
  }
  .toc-content ul {
    margin: 0;
    padding-left: 1.5em;
  }
</style>

<div class="toc-container">
  <details>
    <summary>Contents</summary>
    <div class="toc-content">
      <ul>
        <li><a href="#section-1">Introduction</a></li>
        <li><a href="#section-2">Requirements and Feasibility</a></li>
        <ul>
            <li><a href="#subsection-1-1">Installation of Windows</a></li>
            <li><a href="#subsection-1-1">Installation of Mac</a></li>
            <li><a href="#subsection-1-1">Installation of Linux</a></li>
        </ul>
        <li><a href="#section-3">Interacting with VisIT</a></li>
        <ul>
            <li><a href="#subsection-1-1">Interacting with VisIT GUI</a></li>
            <li><a href="#subsection-1-1">Interacting with VisIT CLI</a></li>
        </ul>
        <li><a href="#section-4">VisIT Elements</a></li>
        <ul>
            <li><a href="#subsection-1-1">Databases</a></li>
            <ul>
                <li><a href="#subsection-1-1">HDF5 data</a></li>
                <li><a href="#subsection-1-1">VTK Files</a></li>
            </ul>
            <li><a href="#subsection-1-1">Plots and Operators</a></li>
            <ul>
                <li><a href="#subsection-1-1">Volume rendering</a></li>
                <li><a href="#subsection-1-1">Isosurface rendering</a></li>
                <li><a href="#subsection-1-1">Vector fields</a></li>
                <li><a href="#subsection-1-1">Streamline integration</a></li>
            </ul>
            <li><a href="#subsection-1-1">Expressions</a></li>
            <li><a href="#subsection-1-1">Exporting Attributes</a></li>
            <li><a href="#subsection-1-1">Finalizing Images</a></li>
        </ul>
        <li><a href="#section-5">Typical VisIT Workflow in Numerical Relativity</a></li>
        <ul>
            <li><a href="#subsection-1-1">Black Holes and Spin Vectors</a></li>
            <li><a href="#subsection-1-1">Density</a></li>
            <ul>
                <li><a href="#subsection-1-1">Isosurface</a></li>
                <li><a href="#subsection-1-1">Volume</a></li>
            </ul>
            <li><a href="#subsection-1-1">Magnetiuc Field Lines</a></li>
            <ul>
                <li><a href="#subsection-1-1">Particle Seeds</a></li>
                <li><a href="#subsection-1-1">Grid Seeds</a></li>
            </ul>
            <li><a href="#subsection-1-1">Fluid Velocity Arrows</a></li>
        </ul>
        <li><a href="#section-6">Case Study: Magnetars</a></li>
        <ul>
            <li><a href="#subsection-1-1">Poloidal Field Lines</a></li>
            <li><a href="#subsection-1-1">Toroidal Field Lines</a></li>
            <li><a href="#subsection-1-1">Final Image and Summary</a></li>
        </ul>
        <li><a href="#section-7">Case Study: Black Hole with Accretion Disk</a></li>
        <ul>
            <li><a href="#subsection-1-1">Isosurface Rendering of Disk</a></li>
            <li><a href="#subsection-1-1">Volume Rendering of Disk</a></li>
            <li><a href="#subsection-1-1">Magnetic Field Lines Around the Black Hole</a></li>
            <li><a href="#subsection-1-1">Visualizing a Relativisitic Jet</a></li>
        </ul>
        <li><a href="#section-8">Case Study: Binary Neutron Stars</a></li>
        <ul>
            <li><a href="#subsection-1-1">Before Black Hole Formation</a></li>
            <li><a href="#subsection-1-1">After Black Hole Formation</a></li>
            <li><a href="#subsection-1-1">Additional Plots After Merger</a></li>
        </ul>
        <li><a href="#section-9">Case Study: Binary Black Hole with Accretion Disk</a></li>
        <ul>
            <li><a href="#subsection-1-1">Magnetic Field Lines in the Disk</a></li>
            <li><a href="#subsection-1-1">Two Black Holes</a></li>
        </ul>
        <li><a href="#section-10">Visualizing Gravitational Waves</a></li>
        <ul>
            <li><a href="#subsection-1-1">Post-Processing Implementation</a></li>
            <li><a href="#subsection-1-1">Three Dimensional Volume Plot</a></li>
            <li><a href="#subsection-1-1">Two Dimensional Contour Plot</a></li>
        </ul>
      </ul>
    </div>
  </details>
</div>

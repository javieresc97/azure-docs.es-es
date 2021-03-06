<!-- A-series - compute-intensive instances, H-series -->

Los tamaños A8-A11 los de la serie H también se conocen como *instancias de proceso intensivo*. El hardware que ejecuta estos tamaños está diseñado y optimizado para aplicaciones de proceso intensivo que consumen muchos recursos de red, incluidas las aplicaciones de clúster de proceso de alto rendimiento (HPC), el modelado y las simulaciones. La serie A8-A11 utiliza Intel Xeon E5-2670 a 2,6 GHz y la serie H, Intel Xeon E5-2667 v3 a 3,2 GHz. 

Las máquinas virtuales de la serie H son las de próxima generación informática de alto rendimiento que abordan las necesidades informáticas de gama alta, como el modelado molecular y la dinámica de fluidos computacional. Estas máquinas virtuales de 8 y 16 núcleos se basan en la tecnología de procesador Intel Haswell E5-2667 V3 con memoria DDR4 y almacenamiento basado en SSD local. 

Además de una potencia de CPU notable, la serie H ofrece varias opciones para las redes RDMA de baja latencia con FDR InfiniBand y varias configuraciones de memoria para admitir requisitos computacionales de uso intensivo de la memoria.



## <a name="h-series"></a>Serie H

ACU: 290-300

| Tamaño | Núcleos de CPU | Memoria: GiB | SSD local: GiB | Discos de datos máx. | Rendimiento de discos máx.: E/S por segundo | Ancho de banda de red/NIC máx. |
| --- | --- | --- | --- | --- | --- | --- |
| Standard_H8 |8 |56 |1000 |16 |16 x 500 |2 / alto |
| Standard_H16 |16 |112 |2000 |32 |32 x 500 |4 / muy alto |
| Standard_H8m |8 |112 |1000 |16 |16 x 500 |2 / alto |
| Standard_H16m |16 |224 |2000 |32 |32 x 500 |4 / muy alto |
| Standard_H16r* |16 |112 |2000 |32 |32 x 500 |4 / muy alto |
| Standard_H16mr* |16 |224 |2000 |32 |32 x 500 |4 / muy alto |

*Compatible con RDMA

<br>



## <a name="a-series---compute-intensive-instances"></a>Serie A: instancias de proceso intensivo

ACU: 225

| Tamaño | Núcleos de CPU | Memoria: GiB | HDD local: GiB | Discos de datos máx. | Rendimiento de discos de datos máx.: E/S por segundo | Ancho de banda de red/NIC máx. |
| --- | --- | --- | --- | --- | --- | --- |
| Standard_A8* |8 |56 |382 |16 |16x500 |2 / alto |
| Standard_A9* |16 |112 |382 |16 |16x500 |4 / muy alto |
| Standard_A10 |8 |56 |382 |16 |16x500 |2 / alto |
| Standard_A11 |16 |112 |382 |16 |16x500 |4 / muy alto |

*Compatible con RDMA

<br>




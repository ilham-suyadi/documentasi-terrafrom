# Penjelasan Mengenai Output

disini output saya gunakan untuk menunjukkan ip yang berhasil didapatkan oleh VMs, untuk menulis script output bisa gunakan beberapa cara masing-masing cara memiliki kelebihan dan kekurangan.

## script pertama

script pertama ini memisahkan masing-masing output VMs, domain name yang digunakan.
***Penulisan Script :***

```sh
output “name_output”{
  value=libvirt_domain.(name_domain).network_interface[0].addresses
}
```

***cth penulisan script :***

```sh
contoh pertama
output "centos_terrafrom_ipaddr" {
    value = libvirt_domain.centos_terraform.network_interface[0].addresses
 }
```

penjelasan:
> name output : adalah nama dari output, kalian bebas memberi nama apapun.
> name domain: adalah nama dari domain yang kalian gunakan di script
> contoh penulisan, saya hanya membuat script dengan nama domain centos_terraform
> nama tersebut tidak dapat digunakan lagi oleh domain yang lain, jika ada yang sama maka script akan menunjukan ke erroran.

kekurangan :
ini sangat tidak efisien ketika membuat banyak VMs

kelebihan :
tapi output dari script ini mudah dibaca

## script kedua

script ini sama sama memisahkan output VMs, tapi domain yang digunakan hanya satu domain untuk membedakannya menggunakan count index.
***penulisan script :***

```sh
 output "(name output)" {
    value = libvirt_domain.(name_domain[number from count.index]).network_interface[0].addresses
 }
```

***cth penulisan script :***

```sh
output "centos_terrafrom_ipaddr" {
    value = libvirt_domain.centos_terraform[0].network_interface[0].addresses
 }

 output "centos_terrafrom_ipaddr_1" {
    value = libvirt_domain.centos_terraform[1].network_interface[0].addresses
 }
```

> name output: adalah nama dari output, kalian bebas memberi nama apapun.
> name domain: adalah nama dari domain yang kalian gunakan di script, untuk count index itu urut dari 0 sampai seterusnya, atau kalian dapat menggunakan terraform state list, dengan catatan kalian sudah apply script terraform
> contoh penulisan, saya membuat beberapa vm yang otomatis yang menggunakan count index, nah [0] dan [1] adalah count index itu sendiri.

kekurangan :
ini sangat tidak efisien ketika membuat banyak VMs

kelebihan :
tapi output dari script ini mudah dibaca

## script ketiga

di script ketiga saya masih menggunakan count index untuk menentukan jumlah vm tapi disini saya membuat output secara otomatis, dalam artian saya tidak perlu memasukkan nomor dari count index.

***penulisan script :***

```sh
output "name output" {
    value = libvirt_domain.(name domain).*.network_interface.0.addresses
 }
```

***cth penulisan :***

```sh
output "centos_terrafrom_ipaddr" {
    value = libvirt_domain.centos_terraform.*.network_interface.0.addresses
 }
```

> name output: adalah nama dari output, kalian bebas memberi nama apapun.
> name domain: adalah nama dari domain yang kalian gunakan di script, nah di script kedua kita menggunakan nomer count index, tapi di script ketiga kita tidak perlu nomor dari count index, tapi kita ganti dengan bintang (*), untuk nama domain dengan bintang dipisahkan oleh tanda titik (.).

kekurangan :
script ini akan memunculkan ip dan name output saja tanpa keterangan apapun, jadi hal tersebut akan menyulitkan dalam hal membaca output.

kelebihan :
ini sangat efektif ketika membuat VMs yang sangat banyak

## script keempat (recomended)

script ini adalah script yang saya gunakan, ini sangat efektif ketika membuat vm yang sangat banyak.

***Penulisan Script :***

```sh
 output "(name_output)" {
   value = tomap({
    for hostname, server in libvirt_domain.(name domain) : hostname => server.network_interface.0.addresses
   })
 }
```

***cth penulisan script :***

```sh
output "ip" {
   value = tomap({
    for hostname, server in libvirt_domain.centos_terraform : hostname => server.network_interface.0.addresses
   })
 }
```

> name output: adalah nama dari output, kalian bebas memberi nama apapun.
> name domain: adalah nama dari domain yang kalian gunakan di script
> hostname : semau bisa diganti dengan name atau yang lain tapi dengan catatan name setelah for dan setelah tanda titik dua (:) harus sama.
> script ini akan menunjukkan ip address dengan count index.

kekurangan :
hanya memberi keterangan dengan count index saya yang dimulai dari 0..

kelebihan :
ini sangat efektif ketika membuat VMs yang sangat banyak
cukup mudah dibaca daripada script ke 3

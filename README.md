# Tugas
TUGAS 10 ARKADEMY
<?php

$host       = "localhost";
$user       = "root";
$pass       = "";
$db         = "Arkademy";

$koneksi    = mysqli_connect($host,$user,$pass,$db);

//cek jika koneksi ke mysql gagal, maka akan tampil pesan berikut
if (!$koneksi){
	die("Gagal melakukan koneksi ke MySQL periksa kembali");
}
$nama_produk    = "";
$jumlah         = "";
$harga          = "";
$keterangan     = "";
$error          = "";
$sukses         = "";

if(isset($_GET['op'])){
    $op = $_GET['op'];
}else{
    $op = "";
}

if($op == 'delete'){
    $id         = $_GET['id'];
    $sql1       = "delete from produk where id = '$id'";
    $q1         = mysqli_query($koneksi,$sql1);
    if($q1){
        $sukses = "Berhasil hapus data";
    }else {
        $error = "Gagal menghapus data";
    }
}

if($op == 'edit'){
    $id             = $_GET['id'];
    $sql1           = "select * from produk where id = '$id'";
    $q1             = mysqli_query($koneksi,$sql1);
    $r1             = mysqli_fetch_array($q1);
    $nama_produk    = $r1['nama_produk'];
    $jumlah         = $r1['jumlah'];
    $harga          = $r1['harga'];
    $keterangan     = $r1['keterangan'];

    if($nama_produk == ''){
        $error = "Data tidak ditemukan";
    }
}

//untuk create
if(isset($_POST['simpan'])){ 
    $nama_produk    = $_POST['nama_produk'];
    $jumlah         = $_POST['jumlah'];
    $harga          = $_POST['harga'];
    $keterangan     = $_POST['keterangan'];

    if ($nama_produk && $jumlah && $harga && $keterangan) {
        if($op == 'edit') { //untuk update
            $sql1     = "update produk set nama_produk = '$nama_produk', jumlah = '$jumlah', harga='$harga',keterangan='$keterangan' where id='$id'";
            $q1       = mysqli_query($koneksi,$sql1);
            if($q1){
                $sukses = "Data Berhasil diupdate";
            }else{
                $error = " Data gagal diupdate";
            }  
        } else { //untuk insert
            $sql1   = "insert into produk(nama_produk,jumlah,harga,keterangan) values('$nama_produk','$jumlah','$harga','$keterangan')";
            $q1     = mysqli_query($koneksi, $sql1);
            if ($q1){
                $sukses     = "Berhasil memasukan data produk";
            } else {
                $error      = "Gagal memasukan data";
            }
        }

    } else{
        $error = "Silakan memasukan semua data";
    }
}
?>
<!DOCTYPE html>
<html lang="en">

<head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Arkademy</title>
    <link href="https://cdn.jsdelivr.net/npm/bootstrap@5.0.1/dist/css/bootstrap.min.css" rel="stylesheet"
        integrity="sha384-+0n0xVW2eSR5OomGNYDnhzAbDsOXxcvSN1TPprVMTNDbiYZCxYbOOl7+AMvyTG2x" crossorigin="anonymous">
    <style>
        .mx-auto {
            width: 800px;
        }

        .card {
            margin-top: 10px;
        }
    </style>
</head>

<body>
    <div class="mx-auto">
        <!--UNTUK MEMASUKAN DATA-->
        <div class="card">
            <div class="card-header">
                Create / Edit Data
            </div>
            <div class="card-body">
                <?php
                if($error) {
                ?>
                    <div class="alert alert-danger" role="alert">
                        <?php echo $error ?>
                    </div>
                <?php
                    header("refresh:8;url=config.php"); // 8 detik
                }
                ?>
                <?php
                 if($sukses) {
                ?>
                    <div class="alert alert-success" role="alert">
                        <?php echo $sukses ?>
                    </div>
                <?php
                    header("refresh:8;url=config.php"); // 8 detik
                }
                ?>
                <form action="" method="POST">
                    <div class="mb-3 row">
                        <label for="nama_produk" class="col-sm-2 col-form-label">Nama Produk</label>
                        <div class="col-sm-10">
                            <input type="text" class="form-control" name="nama_produk"
                                value="<?php echo $nama_produk ?>">
                        </div>
                    </div>
                    <div class="mb-3 row">
                        <label for="jumlah" class="col-sm-2 col-form-label">jumlah</label>
                        <div class="col-sm-10">
                            <input type="text" class="form-control" name="jumlah" id="jumlah"
                                value="<?php echo $jumlah ?>">
                        </div>
                    </div>
                    <div class="mb-3 row">
                        <label for="harga" class="col-sm-2 col-form-label">Harga</label>
                        <div class="col-sm-10">
                            <input type="text" class="form-control" name="harga" id="harga"
                                value="<?php echo $harga ?>">
                        </div>
                    </div>
                    <div class="mb-3 row">
                        <label for="keterangan" class="col-sm-2 col-form-label">Keterangan</label>
                        <div class="col-sm-10">
                            <input type="text" class="form-control" name="keterangan" id="keterangan"
                                value="<?php echo $keterangan ?>">
                        </div>
                    </div>
                    <div class="col-12">
                        <input type="submit" name="simpan" value="Simpan Data" class="btn btn-primary">
                    </div>
                </form>
            </div>
        </div>
        <!--UNTUK MENGELUARKAN DATA-->
        <div class="card">
            <div class="card-header text-white bg-secondary">
                Data Produk
            </div>
            <div class="card-body">
                <table class="table">
                    <thead>
                        <tr>
                            <th scope="col">No</th>
                            <th scope="col">Nama Produk</th>
                            <th scope="col">Jumlah</th>
                            <th scope="col">Harga</th>
                            <th scope="col">Keterangan</th>
                            <th scope="col">Aksi</th>
                        </tr>
                    <tbody>
                        <?php
                            $sql2   = "select * from produk order by id desc";
                            $q2     = mysqli_query($koneksi,$sql2);
                            $urut   = 1;
                            while($r2 = mysqli_fetch_array($q2)){
                                $id             = $r2['id'];
                                $nama_produk    = $r2['nama_produk'];
                                $jumlah         = $r2['jumlah'];
                                $harga          = $r2['harga'];
                                $keterangan     = $r2['keterangan'];

                                ?>
                        <tr>
                            <th scope="row">
                                <?php echo $urut++ ?>
                            </th>
                            <td scope="row">
                                <?php echo $nama_produk ?>
                            </td>
                            <td scope="row">
                                <?php echo $jumlah ?>
                            </td>
                            <td scope="row">
                                <?php echo $harga ?>
                            </td>
                            <td scope="row">
                                <?php echo $keterangan ?>
                            </td>
                            <td scope="row">
                                <a href="config.php?op=edit&id=<?php echo $id?>"><button type="button" class="btn btn-warning">Edit</button></a>
                                <a href="config.php?op=delete&id=<?php echo $id?>" onclick="return confirm('Yakin dihapus?')"><button type="button" class="btn btn-danger">Delete</button></a>
            
                            </td>
                        </tr>
                        <?php
                            }
                            ?>
                    </tbody>
                    </thead>
                </table>
            </div>
        </div>
    </div>
</body>

</html>

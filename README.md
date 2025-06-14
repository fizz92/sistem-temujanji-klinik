
<!DOCTYPE html>
<html lang="ms">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0" />
  <title>Sistem Temujanji Klinik</title>
  <link rel="stylesheet" href="https://cdn.jsdelivr.net/npm/bootstrap@5.3.3/dist/css/bootstrap.min.css" />
  <script src="https://cdnjs.cloudflare.com/ajax/libs/xlsx/0.18.5/xlsx.full.min.js"></script>
  <style>
    body { background-color: #f9f9f9; }
    .container { max-width: 800px; margin-top: 30px; }
    .logo { width: 80px; }
    .table td, .table th { vertical-align: middle; }
  </style>
</head>
<body>
  <div class="container">
    <div class="text-center mb-4">
      <img src="data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAA..." class="logo" alt="KK SIK Logo" />
      <h2>Sistem Temujanji Klinik</h2>
    </div>
    <form id="formTemujanji" class="mb-4">
      <div class="row g-2">
        <div class="col-md-5">
          <input type="text" id="nama" class="form-control" placeholder="Nama Pesakit" required />
        </div>
        <div class="col-md-3">
          <input type="text" id="noDaftar" class="form-control" placeholder="No Daftar" required />
        </div>
        <div class="col-md-4">
          <input type="date" id="tarikh" class="form-control" required />
        </div>
      </div>
      <button type="submit" class="btn btn-success mt-3">Daftar</button>
    </form>

    <input type="text" id="cari" class="form-control mb-3" placeholder="Cari Nama / No Daftar / Tarikh..." />

    <table class="table table-bordered table-striped">
      <thead>
        <tr><th>Nama</th><th>No Daftar</th><th>Tarikh</th><th>Tindakan</th></tr>
      </thead>
      <tbody id="senaraiData"></tbody>
    </table>

    <button class="btn btn-primary" onclick="muatTurunExcel()">Muat Turun Excel</button>
  </div>

  <script>
    let dataPesakit = JSON.parse(localStorage.getItem('dataPesakit')) || [];
    const senaraiData = document.getElementById("senaraiData");

    function paparkanData() {
      senaraiData.innerHTML = '';
      dataPesakit.forEach((item, index) => {
        senaraiData.innerHTML += `
          <tr>
            <td><input value="${item.nama}" onchange="kemaskini(${index}, 'nama', this.value)" /></td>
            <td><input value="${item.noDaftar}" onchange="kemaskini(${index}, 'noDaftar', this.value)" /></td>
            <td><input type="date" value="${item.tarikh}" onchange="kemaskini(${index}, 'tarikh', this.value)" /></td>
            <td><button onclick="padam(${index})" class="btn btn-danger btn-sm">Padam</button></td>
          </tr>`;
      });
    }

    function kemaskini(index, medan, nilai) {
      dataPesakit[index][medan] = nilai;
      simpanData();
    }

    function padam(index) {
      if (confirm("Padam data ini?")) {
        dataPesakit.splice(index, 1);
        simpanData();
      }
    }

    function simpanData() {
      localStorage.setItem('dataPesakit', JSON.stringify(dataPesakit));
      paparkanData();
    }

    document.getElementById("formTemujanji").addEventListener("submit", function(e) {
      e.preventDefault();
      const nama = document.getElementById("nama").value;
      const noDaftar = document.getElementById("noDaftar").value;
      const tarikh = document.getElementById("tarikh").value;
      dataPesakit.push({ nama, noDaftar, tarikh });
      simpanData();
      this.reset();
    });

    document.getElementById("cari").addEventListener("input", function() {
      const nilai = this.value.toLowerCase();
      [...senaraiData.rows].forEach(row => {
        row.style.display = [...row.cells].some(cell =>
          cell.innerText.toLowerCase().includes(nilai)
        ) ? '' : 'none';
      });
    });

    function muatTurunExcel() {
      const ws = XLSX.utils.json_to_sheet(dataPesakit);
      const wb = XLSX.utils.book_new();
      XLSX.utils.book_append_sheet(wb, ws, "Temujanji");
      XLSX.writeFile(wb, "temujanji-klinik.xlsx");
    }

    paparkanData();
  </script>
</body>
</html>

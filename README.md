#include <iostream>
#include <iomanip>
#include <cstring>
#include <fstream>
#include <limits>
using namespace std;

const int MAX = 100;

struct Mahasiswa {
    char Nama[50];
    char Nim[15];
    float IPK;
};

Mahasiswa daftar[MAX];
int jumlahData = 0;

void TambahkanData() {
    char lanjut = 'y';

    while (tolower(lanjut) == 'y') {
        if (jumlahData >= MAX) {
            cout << "Kapasitas data sudah penuh, tidak bisa tambah lagi.\n";
            break;
        }

        cout << "------- Input Data Mahasiswa -------\n";

        // Input Nama
        cout << "Masukkan Nama Mahasiswa : ";
        cin.ignore();
        cin.getline(daftar[jumlahData].Nama, 50);

        // Jika user input "0" untuk batalkan input ini
        if (strcmp(daftar[jumlahData].Nama, "0") == 0) {
            cout << "Input data mahasiswa dibatalkan.\n";
            break;
        }

        // Input NIM
        cout << "Masukkan NIM Mahasiswa  : ";
        cin.getline(daftar[jumlahData].Nim, 15);

        // Input IPK dengan validasi
        float ipk_temp;
        while (true) {
            cout << "Masukkan IPK Mahasiswa  : ";
            cin >> ipk_temp;
            if (cin.fail() || ipk_temp < 0 || ipk_temp > 4.0) {
                cout << "Input IPK tidak valid! IPK harus antara 0 sampai 4.0\n";
                cin.clear();
                cin.ignore(numeric_limits<streamsize>::max(), '\n');
            } else {
                daftar[jumlahData].IPK = ipk_temp;
                cin.ignore(numeric_limits<streamsize>::max(), '\n');
                break;
            }
        }

        jumlahData++;
        cout << "Data mahasiswa berhasil ditambahkan.\n";

        if (jumlahData < MAX) {
            cout << "Tambah data lagi? (y/n): ";
            cin >> lanjut;
            cin.ignore(numeric_limits<streamsize>::max(), '\n');
        } else {
            cout << "Data sudah penuh, tidak bisa tambah lagi.\n";
            break;
        }
    }
}

void TampilkanData() {
    if (jumlahData == 0) {
        cout << "Belum ada data mahasiswa.\n";
        return;
    }

    const int NAMA_WIDTH = 30;
    const int NIM_WIDTH = 20;
    const int IPK_WIDTH = 10;

    cout << "+-" << string(NAMA_WIDTH, '-') << "-+-"
         << string(NIM_WIDTH, '-') << "-+-"
         << string(IPK_WIDTH, '-') << "-+" << endl;

    cout << "| " << left << setw(NAMA_WIDTH) << "Nama Mahasiswa"
         << "| " << left << setw(NIM_WIDTH) << "NIM"
         << "| " << left << setw(IPK_WIDTH) << "IPK" << " |" << endl;

    cout << "+-" << string(NAMA_WIDTH, '-') << "-+-"
         << string(NIM_WIDTH, '-') << "-+-"
         << string(IPK_WIDTH, '-') << "-+" << endl;

    Mahasiswa* ptr = daftar;

    for (int i = 0; i < jumlahData; i++) {
        cout << "| " << left << setw(NAMA_WIDTH) << ptr->Nama
             << "| " << left << setw(NIM_WIDTH) << ptr->Nim
             << "| " << left << setw(IPK_WIDTH) << fixed << setprecision(2) << ptr->IPK << " |" << endl;

        ptr++;  // pindah ke elemen berikutnya
    }

    cout << "+-" << string(NAMA_WIDTH, '-') << "-+-"
         << string(NIM_WIDTH, '-') << "-+-"
         << string(IPK_WIDTH, '-') << "-+" << endl;
}


void CariMahasiswa(const char* kunci) {
    bool ditemukan = false;
    const int NAMA_WIDTH = 30;
    const int NIM_WIDTH = 20;
    const int IPK_WIDTH = 10;

    for (int i = 0; i < jumlahData; i++) {
        if (strcmp(kunci, daftar[i].Nama) == 0 || strcmp(kunci, daftar[i].Nim) == 0) {
            if (!ditemukan) {
                cout << "+-" << string(NAMA_WIDTH, '-') << "-+-"
                     << string(NIM_WIDTH, '-') << "-+-"
                     << string(IPK_WIDTH, '-') << "-+" << endl;

                cout << "| " << left << setw(NAMA_WIDTH) << "Nama Mahasiswa"
                     << "| " << left << setw(NIM_WIDTH) << "NIM"
                     << "| " << left << setw(IPK_WIDTH) << "IPK" << " |" << endl;

                cout << "+-" << string(NAMA_WIDTH, '-') << "-+-"
                     << string(NIM_WIDTH, '-') << "-+-"
                     << string(IPK_WIDTH, '-') << "-+" << endl;

                ditemukan = true;
            }

            cout << "| " << left << setw(NAMA_WIDTH) << daftar[i].Nama
                 << "| " << left << setw(NIM_WIDTH) << daftar[i].Nim
                 << "| " << left << setw(IPK_WIDTH) << fixed << setprecision(2) << daftar[i].IPK << " |" << endl;
        }
    }

    if (ditemukan) {
        cout << "+-" << string(NAMA_WIDTH, '-') << "-+-"
             << string(NIM_WIDTH, '-') << "-+-"
             << string(IPK_WIDTH, '-') << "-+" << endl;
    } else {
        cout << "Data tidak ditemukan.\n";
    }
}

void UrutkanBerdasarkanIPK() {
    for (int i = 0; i < jumlahData - 1; i++) {
        for (int j = 0; j < jumlahData - i - 1; j++) {
            if (daftar[j].IPK < daftar[j + 1].IPK) {
                Mahasiswa temp = daftar[j];
                daftar[j] = daftar[j + 1];
                daftar[j + 1] = temp;
            }
        }
    }
    cout << "Data berhasil diurutkan berdasarkan IPK (tertinggi ke terendah).\n";
}

void SimpanKeFile() {
    if (jumlahData == 0) {
        cout << "Tidak ada data untuk disimpan.\n";
        return;
    }
    
    char namaFile[100];
    
    cout << "--- Simpan Data ke File ---\n";
    cout << "Masukkan nama file untuk menyimpan (contoh: data.txt): ";
    cin.ignore();
    cin.getline(namaFile, 100);
    
    // Pastikan file berekstensi .txt
    char* dot = strrchr(namaFile, '.');
    if (!dot || strcmp(dot, ".txt") != 0) {
        strcat(namaFile, ".txt");
    }
    
    // Cek apakah file sudah ada
    ifstream checkFile(namaFile);
    if (checkFile) {
        checkFile.close();
        char overwrite;
        cout << "File '" << namaFile << "' sudah ada. Apakah Anda ingin menimpa? (y/n): ";
        cin >> overwrite;
        cin.ignore();
        
        if (tolower(overwrite) != 'y') {
            cout << "Penyimpanan dibatalkan.\n";
            return;
        }
    }
    
    ofstream file(namaFile);
    if (!file) {
        cout << "Gagal membuat/membuka file '" << namaFile << "' untuk disimpan.\n";
        return;
    }
    
    for (int i = 0; i < jumlahData; i++) {
        file << daftar[i].Nama << "\n" << daftar[i].Nim << "\n" << daftar[i].IPK << "\n";
    }
    file.close();
    
    cout << "Data berhasil disimpan ke file '" << namaFile << "'\n";
    cout << "Total data yang disimpan: " << jumlahData << " mahasiswa\n";
    
    // Tampilkan preview data yang disimpan
    char lihatData;
    cout << "Apakah Anda ingin melihat data yang disimpan? (y/n): ";
    cin >> lihatData;
    cin.ignore();
    
    if (tolower(lihatData) == 'y') {
        TampilkanData();
    }
}

void LoadDariFile() {
    char namaFile[100];
    
    cout << "--- Load Data dari File ---\n";
    cout << "Masukkan nama file yang ingin dimuat (contoh: data.txt): ";
    cin.ignore();
    cin.getline(namaFile, 100);
    
    char* dot = strrchr(namaFile, '.');
    if (!dot || strcmp(dot, ".txt") != 0) {
        strcat(namaFile, ".txt");
    }
    
    ifstream file(namaFile);
    if (!file) {
        cout << "Gagal membuka file '" << namaFile << "' untuk dibaca.\n";
        cout << "Pastikan file tersebut ada di direktori yang sama dengan program.\n";
        return;
    }
    
    // Backup data lama jika ada
    Mahasiswa backupData[MAX];
    int backupJumlah = jumlahData;
    for (int i = 0; i < jumlahData; i++) {
        backupData[i] = daftar[i];
    }
    
    jumlahData = 0;
    int dataLoaded = 0;
    
    while (file.getline(daftar[jumlahData].Nama, 50) && 
           file.getline(daftar[jumlahData].Nim, 15) &&
           (file >> daftar[jumlahData].IPK)) {
        file.ignore();
        jumlahData++;
        dataLoaded++;
        if (jumlahData >= MAX) break;
    }
    file.close();
    
    if (dataLoaded == 0) {
        cout << "File '" << namaFile << "' kosong atau format tidak valid.\n";
        cout << "Mengembalikan data sebelumnya...\n";
        // Restore backup data
        jumlahData = backupJumlah;
        for (int i = 0; i < backupJumlah; i++) {
            daftar[i] = backupData[i];
        }
        return;
    }
    
    cout << "Data berhasil dimuat dari file '" << namaFile << "'\n";
    cout << "Total data yang dimuat: " << dataLoaded << " mahasiswa\n";
    
    // Tampilkan preview data yang dimuat
    char lihatData;
    cout << "Apakah Anda ingin melihat data yang dimuat? (y/n): ";
    cin >> lihatData;
    cin.ignore();
    
    if (tolower(lihatData) == 'y') {
        TampilkanData();
    }
}

void EditDataMahasiswa() {
    if (jumlahData == 0) {
        cout << "Belum ada data mahasiswa untuk diedit.\n";
        return;
    }
    
    char kunci[50];
    cout << "--- Edit Data Mahasiswa ---\n";
    cout << "Masukkan NIM atau Nama mahasiswa yang ingin diedit: ";
    cin.ignore();
    cin.getline(kunci, 50);
    
    int indexDitemukan = -1;
    for (int i = 0; i < jumlahData; i++) {
        if (strcmp(kunci, daftar[i].Nama) == 0 || strcmp(kunci, daftar[i].Nim) == 0) {
            indexDitemukan = i;
            break;
        }
    }
    
    if (indexDitemukan == -1) {
        cout << "Data mahasiswa tidak ditemukan.\n";
        return;
    }
    
    // Tampilkan data yang akan diedit
    cout << "\nData mahasiswa yang akan diedit:\n";
    cout << "Nama: " << daftar[indexDitemukan].Nama << "\n";
    cout << "NIM : " << daftar[indexDitemukan].Nim << "\n";
    cout << "IPK : " << fixed << setprecision(2) << daftar[indexDitemukan].IPK << "\n\n";
    
    int pilihan;
    cout << "Pilih data yang ingin diedit:\n";
    cout << "1. Edit Nama\n";
    cout << "2. Edit NIM\n";
    cout << "3. Edit IPK\n";
    cout << "4. Edit Semua Data\n";
    cout << "Pilihan: ";
    cin >> pilihan;
    
    switch (pilihan) {
        case 1: {
            cout << "Masukkan nama baru: ";
            cin.ignore();
            cin.getline(daftar[indexDitemukan].Nama, 50);
            cout << "Nama berhasil diubah.\n";
            break;
        }
        case 2: {
            cout << "Masukkan NIM baru: ";
            cin.ignore();
            cin.getline(daftar[indexDitemukan].Nim, 15);
            cout << "NIM berhasil diubah.\n";
            break;
        }
        case 3: {
            float ipk_baru;
            while (true) {
                cout << "Masukkan IPK baru: ";
                cin >> ipk_baru;
                if (cin.fail() || ipk_baru < 0 || ipk_baru > 4.0) {
                    cout << "Input IPK tidak valid! IPK harus antara 0 sampai 4.0\n";
                    cin.clear();
                    cin.ignore(numeric_limits<streamsize>::max(), '\n');
                } else {
                    daftar[indexDitemukan].IPK = ipk_baru;
                    cout << "IPK berhasil diubah.\n";
                    cin.ignore(numeric_limits<streamsize>::max(), '\n');
                    break;
                }
            }
            break;
        }
        case 4: {
            cout << "Masukkan nama baru: ";
            cin.ignore();
            cin.getline(daftar[indexDitemukan].Nama, 50);
            
            cout << "Masukkan NIM baru: ";
            cin.getline(daftar[indexDitemukan].Nim, 15);
            
            float ipk_baru;
            while (true) {
                cout << "Masukkan IPK baru: ";
                cin >> ipk_baru;
                if (cin.fail() || ipk_baru < 0 || ipk_baru > 4.0) {
                    cout << "Input IPK tidak valid! IPK harus antara 0 sampai 4.0\n";
                    cin.clear();
                    cin.ignore(numeric_limits<streamsize>::max(), '\n');
                } else {
                    daftar[indexDitemukan].IPK = ipk_baru;
                    cout << "Semua data berhasil diubah.\n";
                    cin.ignore(numeric_limits<streamsize>::max(), '\n');
                    break;
                }
            }
            break;
        }
        default:
            cout << "Pilihan tidak valid.\n";
            return;
    }
    
    // Tampilkan data setelah diedit
    cout << "\nData setelah diedit:\n";
    cout << "Nama: " << daftar[indexDitemukan].Nama << "\n";
    cout << "NIM : " << daftar[indexDitemukan].Nim << "\n";
    cout << "IPK : " << fixed << setprecision(2) << daftar[indexDitemukan].IPK << "\n";
    
    // Tanya apakah ingin menyimpan ke file
    char simpan;
    cout << "\nApakah Anda ingin menyimpan perubahan ke file? (y/n): ";
    cin >> simpan;
    cin.ignore();
    
    if (tolower(simpan) == 'y') {
        char namaFile[100];
        cout << "Masukkan nama file untuk menyimpan (dengan ekstensi .txt): ";
        cin.getline(namaFile, 100);
        
        char* dot = strrchr(namaFile, '.');
        if (!dot || strcmp(dot, ".txt") != 0) {
            strcat(namaFile, ".txt");
        }
        
        // Simpan ke file dengan nama yang dipilih
        ofstream file(namaFile);
        if (!file) {
            cout << "Gagal membuat/membuka file '" << namaFile << "' untuk disimpan.\n";
            return;
        }
        
        for (int i = 0; i < jumlahData; i++) {
            file << daftar[i].Nama << "\n" << daftar[i].Nim << "\n" << daftar[i].IPK << "\n";
        }
        file.close();
        
        cout << "Data berhasil disimpan ke file '" << namaFile << "'\n";
    }
}

void HapusDataMahasiswa() {
    if (jumlahData == 0) {
        cout << "Belum ada data mahasiswa untuk dihapus.\n";
        return;
    }
    
    char kunci[50];
    cout << "--- Hapus Data Mahasiswa ---\n";
    cout << "Masukkan NIM atau Nama mahasiswa yang ingin dihapus: ";
    cin.ignore();
    cin.getline(kunci, 50);
    
    int indexDitemukan = -1;
    for (int i = 0; i < jumlahData; i++) {
        if (strcmp(kunci, daftar[i].Nama) == 0 || strcmp(kunci, daftar[i].Nim) == 0) {
            indexDitemukan = i;
            break;
        }
    }
    
    if (indexDitemukan == -1) {
        cout << "Data mahasiswa tidak ditemukan.\n";
        return;
    }
    
    // Tampilkan data yang akan dihapus
    cout << "\nData mahasiswa yang akan dihapus:\n";
    cout << "Nama: " << daftar[indexDitemukan].Nama << "\n";
    cout << "NIM : " << daftar[indexDitemukan].Nim << "\n";
    cout << "IPK : " << fixed << setprecision(2) << daftar[indexDitemukan].IPK << "\n\n";
    
    char konfirmasi;
    cout << "Apakah Anda yakin ingin menghapus data ini? (y/n): ";
    cin >> konfirmasi;
    cin.ignore();
    
    if (tolower(konfirmasi) == 'y') {
        // Geser semua data setelah index yang dihapus ke kiri
        for (int i = indexDitemukan; i < jumlahData - 1; i++) {
            daftar[i] = daftar[i + 1];
        }
        jumlahData--;
        cout << "Data berhasil dihapus.\n";
        
        // Tanya apakah ingin menyimpan perubahan ke file
        char simpan;
        cout << "\nApakah Anda ingin menyimpan perubahan ke file? (y/n): ";
        cin >> simpan;
        cin.ignore();
        
        if (tolower(simpan) == 'y') {
            char namaFile[100];
            cout << "Masukkan nama file untuk menyimpan (dengan ekstensi .txt): ";
            cin.getline(namaFile, 100);
            
            // Pastikan file berekstensi .txt
            char* dot = strrchr(namaFile, '.');
            if (!dot || strcmp(dot, ".txt") != 0) {
                strcat(namaFile, ".txt");
            }
            
            // Simpan ke file dengan nama yang dipilih
            ofstream file(namaFile);
            if (!file) {
                cout << "Gagal membuat/membuka file '" << namaFile << "' untuk disimpan.\n";
                return;
            }
            
            for (int i = 0; i < jumlahData; i++) {
                file << daftar[i].Nama << "\n" << daftar[i].Nim << "\n" << daftar[i].IPK << "\n";
            }
            file.close();
            
            cout << "Data berhasil disimpan ke file '" << namaFile << "'\n";
        }
    } else {
        cout << "Penghapusan data dibatalkan.\n";
    }
}

void HapusFile() {
    char namaFile[100];
    
    cout << "--- Hapus File Data ---\n";
    cout << "Masukkan nama file yang ingin dihapus (contoh: data.txt): ";
    cin.ignore();
    cin.getline(namaFile, 100);
    
    // Pastikan file berekstensi .txt
    char* dot = strrchr(namaFile, '.');
    if (!dot || strcmp(dot, ".txt") != 0) {
        strcat(namaFile, ".txt");
    }
    
    // Cek apakah file ada
    ifstream checkFile(namaFile);
    if (!checkFile) {
        cout << "File '" << namaFile << "' tidak ditemukan.\n";
        return;
    }
    checkFile.close();
    
    char konfirmasi;
    cout << "Anda akan menghapus file '" << namaFile << "' secara permanen.\n";
    cout << "Apakah Anda yakin? (y/n): ";
    cin >> konfirmasi;
    cin.ignore();
    
    if (tolower(konfirmasi) == 'y') {
        if (remove(namaFile) == 0) {
            cout << "File '" << namaFile << "' berhasil dihapus.\n";
        } else {
            cout << "Gagal menghapus file '" << namaFile << "'.\n";
        }
    } else {
        cout << "Penghapusan file dibatalkan.\n";
    }
}
int main() {
    int pilihan;
    char kunci[50];

    do {
        cout << "\n--- Sistem Informasi Nilai Mahasiswa ---\n";
        cout << "1. Tambah Data Mahasiswa\n";
        cout << "2. Tampilkan Semua Mahasiswa\n";
        cout << "3. Cari Mahasiswa (NIM/Nama)\n";
        cout << "4. Urutkan Mahasiswa Berdasarkan Nilai\n";
        cout << "5. Simpan ke File\n";
        cout << "6. Load dari File\n";
        cout << "7. Edit Data Mahasiswa\n";
        cout << "8. Hapus Data Mahasiswa\n";
        cout << "9. Hapus File Data\n";
        cout << "0. Keluar\n";
        cout << "Pilihan: ";
        cin >> pilihan;

        switch (pilihan) {
            case 1:
                system("cls");
                TambahkanData();
                break;
            case 2:
                system("cls");
                TampilkanData();
                break;
            case 3:
                system("cls");
                cout << "Masukkan nama atau NIM mahasiswa: ";
                cin.ignore();
                cin.getline(kunci, 50);
                CariMahasiswa(kunci);
                break;
            case 4:
                system("cls");
                UrutkanBerdasarkanIPK();
                break;
            case 5:
                system("cls");
                SimpanKeFile();
                break;
            case 6:
                system("cls");
                LoadDariFile();
                break;
            case 7:
                system("cls");
                EditDataMahasiswa();
                break;
             case 8:
                system("cls");
                HapusDataMahasiswa();
                break;
            case 9:
                system("cls");
                HapusFile();
                break;
            case 0:
                system("cls");
                cout << "Keluar dari program.\n";
                break;
            default:
                cout << "Menu yang Anda pilih tidak valid.\n";
        }
    } while (pilihan != 0);

    return 0;
}

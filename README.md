# Local Big Data Irish Meter (Time Series)

![Workflow](https://github.com/DJahvoe/Local_Big_Data_Irish_Meter/blob/master/screenshot/workflow.jpg)

## _Business Understanding_

### Kemungkinan proses yang dapat dilakukan dari data

- Mengetahui penggunaan listrik yang terdapat di Irlandia.
- Melakukan analisis serta klasifikasi pada runtutan waktu (time series) penggunaan listrik di Irlandia.

## _Data Understanding_

![Data Set](https://github.com/DJahvoe/Local_Big_Data_Irish_Meter/blob/master/screenshot/file_reader.jpg)

- dataset merupakan data meteran listrik di Irlandia.
- jumlah data yang digunakan adalah sebanyak **1.226.830 baris**
- **meterID**: merupakan **identitas** dari suatu meteran listrik di Irlandia.
- **enc_datetime**: merupakan **waktu** tepat saat meteran listrik sedang dibaca.
- **reading**: merupakan **besar listrik** yang digunakan meteran dalam pembacaan.

## _Data Preparation_

![File Reader](https://github.com/DJahvoe/Local_Big_Data_Irish_Meter/blob/master/screenshot/file_reader.jpg)

1. **Setup environment dan membaca data** <br>
   ![DataPrep1](https://github.com/DJahvoe/Local_Big_Data_Irish_Meter/blob/master/screenshot/data_preparation_1.jpg)<br>

   - Membaca data menggunakan **File Reader** <br>
     ![Environment Setup](https://github.com/DJahvoe/Local_Big_Data_Irish_Meter/blob/master/screenshot/file_reader.jpg)<br>
   - Membuat Big Data Environment menggunakan **Create Local Big Data Environment** <br>
     ![Create Local Big Data](https://github.com/DJahvoe/Local_Big_Data_Irish_Meter/blob/master/screenshot/create_local_big_data_environment.jpg)<br>

2. **Melakukan load data** <br>
   ![DataPrep2](https://github.com/DJahvoe/Local_Big_Data_Irish_Meter/blob/master/screenshot/data_preparation_2.jpg)<br>
   Isi dari metanode **Load Data**: <br>
   ![LoadData](https://github.com/DJahvoe/Local_Big_Data_Irish_Meter/blob/master/screenshot/metanode_load_data.jpg)<br>

   - Membuat koneksi database menggunakan **DB Table Creator**.
   - Load data tabel dari database menggunakan **DB Loader**.
   - Hasil dari query hive dapat dibaca pada Spark Dataframe dengan mengkonversi menggunakan **Hive to Spark**.

3. **Ekstraksi atribut date-time** <br>
   ![DataPrep3](https://github.com/DJahvoe/Local_Big_Data_Irish_Meter/blob/master/screenshot/data_preparation_3.jpg)<br>
   Isi dari metanode **Extract date-time attributes**:<br>
   ![LoadData](https://github.com/DJahvoe/Local_Big_Data_Irish_Meter/blob/master/screenshot/metanode_extract_date_time_attributes.jpg)<br>
   Mengeksekusi query menggunakan **Spark SQL Query**

   - Mengkonversi datetime dengan query sebagai berikut:<br>
     ![Initial Datetime](https://github.com/DJahvoe/Local_Big_Data_Irish_Meter/blob/master/screenshot/sql_initial_datetime.jpg)<br>
     Hasil dari Query: <br>
     ![Initial Datetime Result](https://github.com/DJahvoe/Local_Big_Data_Irish_Meter/blob/master/screenshot/sql_initial_datetime_result.jpg)<br>
   - Mengekstrasi detail waktu dari **eventData**: <br>
     ![Detail Waktu](https://github.com/DJahvoe/Local_Big_Data_Irish_Meter/blob/master/screenshot/sql_extract_new_datetime_features.jpg)<br>
     Hasil dari Query: <br>
     ![Detail Waktu Result](https://github.com/DJahvoe/Local_Big_Data_Irish_Meter/blob/master/screenshot/sql_extract_new_datetime_features_result.jpg)<br>
   - Mengklasifikasi data **weekends** dan **weekdays**: <br>
     ![Klasifikasi Weekend Weekday](https://github.com/DJahvoe/Local_Big_Data_Irish_Meter/blob/master/screenshot/sql_assign_weekend_weekday.jpg)<br>
     Hasil dari Query: <br>
     ![Klasifikasi Weekend Weekday Result](https://github.com/DJahvoe/Local_Big_Data_Irish_Meter/blob/master/screenshot/sql_assign_weekend_weekday_result.jpg)<br>
   - Membagi data jam per segment: <br>
     ![Assignment hour segment](https://github.com/DJahvoe/Local_Big_Data_Irish_Meter/blob/master/screenshot/sql_assign_hourly_bins.jpg)<br>
     Hasil dari Query: <br>
     ![Assignment hour segment result](https://github.com/DJahvoe/Local_Big_Data_Irish_Meter/blob/master/screenshot/sql_assign_hourly_bins_result.jpg)<br>

4. **Fungsi agregasi dan time series** <br>
   ![DataPrep4](https://github.com/DJahvoe/Local_Big_Data_Irish_Meter/blob/master/screenshot/data_preparation_4.jpg)<br>
   Isi dari metanode **Aggregations and time series**<br>
   ![Aggregation1](https://github.com/DJahvoe/Local_Big_Data_Irish_Meter/blob/master/screenshot/metanode_aggregation_1.jpg)<br>
   ![Aggregation2](https://github.com/DJahvoe/Local_Big_Data_Irish_Meter/blob/master/screenshot/metanode_aggregation_2.jpg)<br>

   - Sebelum dimasukkan ke dalam fungsi agregasi, data akan melewati **Persist Spark DataFrame/RDD** untuk mempercepat akses karena adanya caching<br>
   - Beberapa fungsi agregasi dan time series:
     - Mencari fungsi total penggunaan listrik<br>
       ![1](https://github.com/DJahvoe/Local_Big_Data_Irish_Meter/blob/master/screenshot/Aggregation/1.jpg)<br>
       ![1_1](https://github.com/DJahvoe/Local_Big_Data_Irish_Meter/blob/master/screenshot/Aggregation/1_1.jpg)<br>
       penggunaan fungsi agregasi **SUM** dengan pengelompokkan berdasarkan **meterID**<br>
     - Mencari rata-rata penggunaan listrik per tahun<br>
       ![2](https://github.com/DJahvoe/Local_Big_Data_Irish_Meter/blob/master/screenshot/Aggregation/2.jpg)<br>
       ![2_1](https://github.com/DJahvoe/Local_Big_Data_Irish_Meter/blob/master/screenshot/Aggregation/2_1.jpg)<br>
       penggunaan fungsi agregasi **SUM** dengan pengelompokkan berdasarkan **meterID, year**<br>
       ![2_2](https://github.com/DJahvoe/Local_Big_Data_Irish_Meter/blob/master/screenshot/Aggregation/2_2.jpg)<br>
       penggunaan fungsi agregasi **MEAN** dengan pengelompokkan berdasarkan **meterID**<br>
     - Mencari rata-rata penggunaan listrik per bulan<br>
       ![3](https://github.com/DJahvoe/Local_Big_Data_Irish_Meter/blob/master/screenshot/Aggregation/3.jpg)<br>
       ![3_1](https://github.com/DJahvoe/Local_Big_Data_Irish_Meter/blob/master/screenshot/Aggregation/3_1.jpg)<br>
       penggunaan fungsi agregasi **SUM** dengan pengelompokkan berdasarkan **meterID, year, month**<br>
       ![3_2](https://github.com/DJahvoe/Local_Big_Data_Irish_Meter/blob/master/screenshot/Aggregation/3_2.jpg)<br>
       penggunaan fungsi agregasi **MEAN** dengan pengelompokkan berdasarkan **meterID**<br>
     - Mencari rata-rata penggunaan listrik per minggu<br>
       ![4](https://github.com/DJahvoe/Local_Big_Data_Irish_Meter/blob/master/screenshot/Aggregation/4.jpg)<br>
       ![4_1](https://github.com/DJahvoe/Local_Big_Data_Irish_Meter/blob/master/screenshot/Aggregation/4_1.jpg)<br>
       penggunaan fungsi agregasi **SUM** dengan pengelompokkan berdasarkan **meterID, year, week**<br>
       ![4_2](https://github.com/DJahvoe/Local_Big_Data_Irish_Meter/blob/master/screenshot/Aggregation/4_2.jpg)<br>
       penggunaan fungsi agregasi **MEAN** dengan pengelompokkan berdasarkan **meterID**<br>
     - Mencari rata-rata penggunaan listrik per hari dalam seminggu<br>
       ![5](https://github.com/DJahvoe/Local_Big_Data_Irish_Meter/blob/master/screenshot/Aggregation/5.jpg)<br>
       ![5_1](https://github.com/DJahvoe/Local_Big_Data_Irish_Meter/blob/master/screenshot/Aggregation/5_1.jpg)<br>
       penggunaan fungsi agregasi **SUM** dengan pengelompokkan berdasarkan **meterID, year, week, dayOfWeek**<br>
       ![5_2](https://github.com/DJahvoe/Local_Big_Data_Irish_Meter/blob/master/screenshot/Aggregation/5_2.jpg)<br>
       penggunaan fungsi agregasi **MEAN** dengan pengelompokkan berdasarkan **meterID** serta pivot dari kolom **dayOfWeek**<br>
     - Mencari rata-rata penggunaan listrik per hari<br>
       ![6](https://github.com/DJahvoe/Local_Big_Data_Irish_Meter/blob/master/screenshot/Aggregation/6.jpg)<br>
       ![6_1](https://github.com/DJahvoe/Local_Big_Data_Irish_Meter/blob/master/screenshot/Aggregation/6_1.jpg)<br>
       penggunaan fungsi agregasi **SUM** dengan pengelompokkan berdasarkan **meterID, eventDate**<br>
       ![6_2](https://github.com/DJahvoe/Local_Big_Data_Irish_Meter/blob/master/screenshot/Aggregation/6_2.jpg)<br>
       penggunaan fungsi agregasi **MEAN** dengan pengelompokkan berdasarkan **meterID**<br>
     - Mencari rata-rata penggunaan listrik pada segmen jam per hari<br>
       ![7](https://github.com/DJahvoe/Local_Big_Data_Irish_Meter/blob/master/screenshot/Aggregation/7.jpg)<br>
       ![7_1](https://github.com/DJahvoe/Local_Big_Data_Irish_Meter/blob/master/screenshot/Aggregation/7_1.jpg)<br>
       penggunaan fungsi agregasi **SUM** dengan pengelompokkan berdasarkan **meterID, eventDate, daySegment**<br>
       ![7_2](https://github.com/DJahvoe/Local_Big_Data_Irish_Meter/blob/master/screenshot/Aggregation/7_2.jpg)<br>
       penggunaan fungsi agregasi **MEAN** dengan pengelompokkan berdasarkan **meterID** serta pivot dari kolom **daySegment**<br>
     - Mencari rata-rata penggunaan listrik pada weekday dan weekend<br>
       ![8](https://github.com/DJahvoe/Local_Big_Data_Irish_Meter/blob/master/screenshot/Aggregation/8.jpg)<br>
       ![8_1](https://github.com/DJahvoe/Local_Big_Data_Irish_Meter/blob/master/screenshot/Aggregation/8_1.jpg)<br>
       penggunaan fungsi agregasi **SUM** dengan pengelompokkan berdasarkan **meterID, year, month, week, dayClassifier**<br>
       ![8_2](https://github.com/DJahvoe/Local_Big_Data_Irish_Meter/blob/master/screenshot/Aggregation/8_2.jpg)<br>
       penggunaan fungsi agregasi **MEAN** dengan pengelompokkan berdasarkan **meterID** serta pivot dari kolom **dayClassifier**<br>
     - Mencari rata-rata penggunaan listrik per jam<br>
       ![9](https://github.com/DJahvoe/Local_Big_Data_Irish_Meter/blob/master/screenshot/Aggregation/9.jpg)<br>
       ![9_1](https://github.com/DJahvoe/Local_Big_Data_Irish_Meter/blob/master/screenshot/Aggregation/9_1.jpg)<br>
       penggunaan fungsi agregasi **SUM** dengan pengelompokkan berdasarkan **meterID, eventDate, hour**<br>
       ![9_2](https://github.com/DJahvoe/Local_Big_Data_Irish_Meter/blob/master/screenshot/Aggregation/9_2.jpg)<br>
       penggunaan fungsi agregasi **MEAN** dengan pengelompokkan berdasarkan **meterID**<br>
   - Setelah tiap proses fungsi agregasi dieksekusi, dilakukan rename dengan menggunakan **Spark Column Rename** sesuai kebutuhan.<br>
   - Setelah berhasil di-rename, dilakukan penggabungan hasil agregasi menjadi 1 tabel menggunakan **Spark Joiner**<br>
     ![joiner1](https://github.com/DJahvoe/Local_Big_Data_Irish_Meter/blob/master/screenshot/Aggregation/joiner1.jpg)<br>
     ![joiner2](https://github.com/DJahvoe/Local_Big_Data_Irish_Meter/blob/master/screenshot/Aggregation/joiner2.jpg)<br>

5. **Membuat persentase per hari, serta persentase segmen jam tiap harinya** <br>
   ![percentage](https://github.com/DJahvoe/Local_Big_Data_Irish_Meter/blob/master/screenshot/sql_compute_daily.jpg)<br>
   eksekusi dilakukan dengan menggunakan **Spark SQL Query**<br>

6. **Hasil dari data preparation didapatkan dengan kolom tabel sebagai berikut**<br>
   ![table_column_preparation](https://github.com/DJahvoe/Local_Big_Data_Irish_Meter/blob/master/screenshot/table_column_preparation.jpg)<br>

7. **Output dari SQL Query menjadi input dari metanode _PCA, K-means, Scatter Plot_**<br>
   ![sql query output](https://github.com/DJahvoe/Local_Big_Data_Irish_Meter/blob/master/screenshot/spark_sql_query_output.jpg)<br>
   dengan node-nodenya sebagai berikut<br>
   ![pca kmeans scatter](https://github.com/DJahvoe/Local_Big_Data_Irish_Meter/blob/master/screenshot/metanode_pca_kmeans_scatter.jpg)<br>

## _Modelling_

![Model](https://github.com/DJahvoe/Local_Big_Data_Irish_Meter/blob/master/screenshot/modelling.jpg)

### Proses modelling:

1. Data yang telah diinput akan dinormalisasi terlebih dahulu dengan **nilai minimal 0** dan **nilai maksimal 1** menggunakan **Spark Normalizer**.
2. **Spark PCA** akan mengurangi dimensi dari data yang diinput serta **Spark k-Means** akan membuat cluster dengan algoritma **k-Means**<br>
   ![pca](https://github.com/DJahvoe/Local_Big_Data_Irish_Meter/blob/master/screenshot/pca.jpg)<br>
   ![kmean](https://github.com/DJahvoe/Local_Big_Data_Irish_Meter/blob/master/screenshot/kmean.jpg)<br>
3. Data pada **Spark k-Means** selanjutnya akan di-filter menggunakan **Spark Column Filter** untuk mendapatkan kolom **meterID** dan hasil **Cluster** saja.<br>
   ![col_filter](https://github.com/DJahvoe/Local_Big_Data_Irish_Meter/blob/master/screenshot/col_filter.jpg)<br>
4. Data tabel hasil eksekusi dari **Spark PCA** dan **Spark k-Means** yang telah di-filter selanjutnya digabungkan dengan menggunakan **Spark Joiner**
5. Agar dapat dibaca menggunakan node KNIME, Data harus dikonversi dari Spark Dataframe menjadi Table menggunakan **Spark to Table**
6. Hasil konversi kemudian dikembalikan nilai rangenya menggunakan **Denormalizer (PMML)**.
7. Didapatkan data tabel dari hasil tahap modelling sebagai berikut<br>
   ![denorm](https://github.com/DJahvoe/Local_Big_Data_Irish_Meter/blob/master/screenshot/denorm.jpg)<br>

## _Evaluation_

![Evaluation](https://github.com/DJahvoe/Local_Big_Data_Irish_Meter/blob/master/screenshot/evaluation.jpg)<br>
Model dievaluasi menggunakan **Scatter Plot** dan **Table View** dengan cara

1. Data hasil proses modelling selanjutnya dikonversi menjadi string menggunakan **Number To String** agar mudah untuk melakukan plotting
2. Dilakukan assignment warna pada **Color Manager** untuk menampilkan cluster dengan jelas<br>
   ![colormanager](https://github.com/DJahvoe/Local_Big_Data_Irish_Meter/blob/master/screenshot/colormanager.jpg)<br>
3. Tampilan yang didapatkan pada **Scatter Plot**<br>
   ![scatterplot](https://github.com/DJahvoe/Local_Big_Data_Irish_Meter/blob/master/screenshot/scatterplot.jpg)<br>
4. Tampilan yang didapatkan pada **Table View**<br>
   ![tableview](https://github.com/DJahvoe/Local_Big_Data_Irish_Meter/blob/master/screenshot/tableview.jpg)<br>

Kesimpulan:<br>
Dari visualisasi yang didapatkan melalui **Scatter Plot** bisa dilihat bahwa cluster0 memiliki korelasi yang dekat dengan cluster2 dengan melihat jarak pusat cluster yang berdekatan, sedangkan untuk cluster1 sangat jauh dari cluster0 maupun cluster2, sehingga kemungkinan besar cluster1 dengan cluster yang lainnya tidak memiliki kemiripan.

## _Deployment_

![parquet](https://github.com/DJahvoe/Local_Big_Data_Irish_Meter/blob/master/screenshot/parquet.jpg)<br>
![hasilexport](https://github.com/DJahvoe/Local_Big_Data_Irish_Meter/blob/master/screenshot/hasilexport.jpg)<br>
Pada tahap deployment digunakan **Spark to Parquet** agar Spark dataframe dapat dikonversi menjadi bentuk parquet file.

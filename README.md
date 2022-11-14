# Lab-management-system-
Lab Management System This project is mainly designed to maintain the overall details of labs in any educational institutes and developed using HTML, CSS, JavaScript, MySQL, Python Flask.
from flask import Flask, render_template, request, redirect, url_for, flash
from flask_mysqldb import MySQL



app = Flask(__name__)
app.secret_key = 'many random bytes'

app.config['MYSQL_HOST'] = 'localhost'
app.config['MYSQL_USER'] = 'root'
app.config['MYSQL_PASSWORD'] = ''
app.config['MYSQL_DB'] = 'lms'

mysql = MySQL(app)


#--------------------------------Home Page Start-----------------------------------------#

@app.route('/')
def Index():
    return render_template('index.html')

@app.route('/login', methods = ['POST'])
def login():
    try:
        if request.method == "POST":
            sno = request.form['sno']
            usn = request.form['usn']
            cur = mysql.connection.cursor()
            cur.execute("INSERT INTO login (sno, usn, timestamp) VALUES (%s, %s, CURRENT_TIMESTAMP())", (sno, usn,))
            mysql.connection.commit()
            flash("Login Successfully")
            return render_template('welcome.html')
    except:
        flash("Login Unsuccessfully")
        return redirect(url_for('Index'))

@app.route('/displogin')
def displogin():
    cur = mysql.connection.cursor()
    cur.execute("SELECT  * FROM login")
    data = cur.fetchall()
    cur.close()
    return render_template('displogin.html', system=data )


@app.route('/dispdelete/<string:id_data>', methods = ['GET'])
def dispdelete(id_data):
    try:
        cur = mysql.connection.cursor()
        cur.execute("DELETE FROM login WHERE usn=%s", (id_data,))
        mysql.connection.commit()
        flash("Record Has Been Deleted Successfully")
        return redirect(url_for('displogin'))
    except:
        flash("Record Has Not Been Deleted Successfully")
        return redirect(url_for('displogin'))
            
#-------------------------------Admin Login-------------------------------------------#
@app.route('/alogin', methods = ['POST'])
def alogin():
    try:
        if request.method == "POST":
            username = request.form['username']
            password = request.form['password']
            if(username=="admin" and password=="admin"):
                flash("Login Successfully")
                return redirect(url_for('sindex'))
            else:
                flash('Login Failed')
                return redirect(url_for('Index'))
    except:
        flash("Login Unsuccessfully")
        return redirect(url_for('Index'))


#--------------------------------System Start-----------------------------------------#
@app.route('/sindex')
def sindex():
    cur = mysql.connection.cursor()
    cur.execute("SELECT  * FROM systems")
    data = cur.fetchall()
    cur.close()
    return render_template('sindex.html', system=data )


@app.route('/insert', methods = ['POST'])
def insert():
    try:
        if request.method == "POST":
            sno = request.form['sno']
            scondition = request.form['scondition']
            sconfiguration = request.form['sconfiguration']
            rno = request.form['rno']
            cur = mysql.connection.cursor()
            cur.execute("INSERT INTO systems (sno, scondition, sconfiguration, rno) VALUES (%s, %s, %s, %s)", (sno, scondition, sconfiguration, rno))
            mysql.connection.commit()
            flash("Data Inserted Successfully")
            return redirect(url_for('sindex'))
    except:
        flash("Data Insertion Unsuccessfully")
        return redirect(url_for('sindex'))



@app.route('/delete/<string:id_data>', methods = ['GET'])
def delete(id_data):
    try:
        cur = mysql.connection.cursor()
        cur.execute("DELETE FROM systems WHERE sno=%s", (id_data,))
        mysql.connection.commit()
        flash("Record Has Been Deleted Successfully")
        return redirect(url_for('sindex'))
    except:
        flash("Record Has Not Been Deleted Successfully")
        return redirect(url_for('sindex'))



@app.route('/update',methods=['POST','GET'])
def update():
    try:
        if request.method == 'POST':
            sno = request.form['sno']
            scondition = request.form['scondition']
            sconfiguration = request.form['sconfiguration']
            rno = request.form['rno']
            cur = mysql.connection.cursor()
            cur.execute("""
                UPDATE systems
                SET scondition=%s, sconfiguration=%s, rno=%s
                WHERE sno=%s
                """, (scondition, sconfiguration, rno, sno))
            mysql.connection.commit()
            flash("Data Updated Successfully")
            return redirect(url_for('sindex'))
    except:
        flash("Data Updation Unsuccessfully")
        return redirect(url_for('sindex'))

#--------------------------------Lab Start-----------------------------------------#

@app.route('/lindex')
def lindex():
    cur = mysql.connection.cursor()
    cur.execute("SELECT * FROM lab")
    data = cur.fetchall()
    cur.close()
    return render_template('lindex.html', system=data )


@app.route('/linsert', methods = ['POST'])
def linsert():
    try:
        if request.method == "POST":
            rno = request.form['rno']
            lname = request.form['lname']
            lcapacity = request.form['lcapacity']
            wifi = request.form['wifi']
            projector = request.form['projector']
            chairs = request.form['chairs']
            no_of_chairs = request.form['no_of_chairs']
            cur = mysql.connection.cursor()
            cur.execute("INSERT INTO lab (rno, lname, lcapacity, wifi, projector, chairs, no_of_chairs) VALUES (%s, %s, %s, %s, %s, %s, %s)"
            ,(rno, lname, lcapacity, wifi, projector, chairs, no_of_chairs))
            mysql.connection.commit()
            flash("Data Inserted Successfully")
            return redirect(url_for('lindex'))
    except:
        flash("Data Insertion Unsuccessfully")
        return redirect(url_for('lindex'))



@app.route('/ldelete/<string:id_data>', methods = ['GET'])
def ldelete(id_data):
    try:
        cur = mysql.connection.cursor()
        cur.execute("DELETE FROM lab WHERE rno=%s", (id_data,))
        mysql.connection.commit()
        flash("Record Has Been Deleted Successfully")
        return redirect(url_for('lindex'))
    except:
        flash("Record Has Not Been Deleted Successfully")
        return redirect(url_for('lindex'))



@app.route('/lupdate',methods=['POST','GET'])
def lupdate():
    try:
        if request.method == 'POST':
            rno = request.form['rno']
            lname = request.form['lname']
            lcapacity = request.form['lcapacity']
            wifi = request.form['wifi']
            projector = request.form['projector']
            chairs = request.form['chairs']
            no_of_chairs = request.form['no_of_chairs']
            cur = mysql.connection.cursor()
            cur.execute("""
                UPDATE lab SET
                lname=%s, lcapacity=%s, wifi=%s, projector=%s, chairs=%s, no_of_chairs=%s
                WHERE rno=%s
                """, (lname, lcapacity, wifi, projector, chairs, no_of_chairs, rno))
            mysql.connection.commit()
            flash("Data Updated Successfully")
            return redirect(url_for('lindex'))
    except:
        flash("Data Updation Unsuccessfully")
        return redirect(url_for('lindex'))

#--------------------------------Subject Start-----------------------------------------#

@app.route('/subindex')
def subindex():
    cur = mysql.connection.cursor()
    cur.execute("SELECT  * FROM subject")
    data = cur.fetchall()
    cur.close()
    return render_template('subindex.html', system=data )


@app.route('/subinsert', methods = ['POST'])
def subinsert():
    try:
        if request.method == "POST":
            sbcode = request.form['sbcode']
            sbname = request.form['sbname']
            sbbranch = request.form['sbbranch']
            sbcredits = request.form['sbcredits']
            sbsem = request.form['sbsem']
            cur = mysql.connection.cursor()
            cur.execute("INSERT INTO subject (sbcode, sbname, sbbranch,sbcredits,sbsem) VALUES (%s, %s, %s, %s, %s)", (sbcode, sbname, sbbranch,sbcredits,sbsem))
            mysql.connection.commit()
            flash("Data Inserted Successfully")
            return redirect(url_for('subindex'))
    except:
        flash("Data Insertion Unsuccessfully")
        return redirect(url_for('subindex'))



@app.route('/subdelete/<string:id_data>', methods = ['GET'])
def subdelete(id_data):
    try:
        cur = mysql.connection.cursor()
        cur.execute("DELETE FROM subject WHERE sbcode=%s", (id_data,))
        mysql.connection.commit()
        flash("Record Has Been Deleted Successfully")
        return redirect(url_for('subindex'))
    except:
        flash("Record Has Not Been Deleted Successfully")
        return redirect(url_for('subindex'))



@app.route('/subupdate',methods=['POST','GET'])
def subupdate():
    try:
        if request.method == 'POST':
            sbcode = request.form['sbcode']
            sbname = request.form['sbname']
            sbbranch = request.form['sbbranch']
            sbcredits = request.form['sbcredits']
            sbsem = request.form['sbsem']
            cur = mysql.connection.cursor()
            cur.execute("""
                UPDATE subject
                SET sbname=%s ,sbbranch=%s, sbcredits=%s,sbsem=%s
                WHERE sbcode=%s
                """, (sbname, sbbranch,sbcredits,sbsem,sbcode))
            mysql.connection.commit()
            flash("Data Updated Successfully")
            return redirect(url_for('subindex'))
    except:
        flash("Data Updation Unsuccessfully")
        return redirect(url_for('subindex'))

#--------------------------------Teacher Start-----------------------------------------#

@app.route('/tindex')
def tindex():
    cur = mysql.connection.cursor()
    cur.execute("SELECT  * FROM lecturer")
    data = cur.fetchall()
    cur.close()
    return render_template('tindex.html', system=data )


@app.route('/tinsert', methods = ['POST'])
def tinsert():
    try:
        if request.method == "POST":
            lsn = request.form['lsn']
            name = request.form['name']
            gender = request.form['gender']
            email = request.form['email']
            sbcode = request.form['sbcode']
            rno = request.form['rno']
            cur = mysql.connection.cursor()
            cur.execute("INSERT INTO lecturer (lsn,name,gender,email,sbcode,rno) VALUES (%s, %s, %s, %s, %s,%s)", (lsn,name,gender,email,sbcode,rno))
            mysql.connection.commit()
            flash("Data Inserted Successfully")
            return redirect(url_for('tindex'))
    except:
        flash("Data Insertion Unsuccessfully")
        return redirect(url_for('tindex'))



@app.route('/tdelete/<string:id_data>', methods = ['GET'])
def tdelete(id_data):
    try:
        cur = mysql.connection.cursor()
        cur.execute("DELETE FROM lecturer WHERE lsn=%s", (id_data,))
        mysql.connection.commit()
        flash("Record Has Been Deleted Successfully")
        return redirect(url_for('tindex'))
    except:
        flash("Record Has Not Been Deleted Successfully")
        return redirect(url_for('tindex'))



@app.route('/tupdate',methods=['POST','GET'])
def tupdate():
    try:
        if request.method == 'POST':
            lsn = request.form['lsn']
            name = request.form['name']
            gender = request.form['gender']
            email = request.form['email']
            sbcode = request.form['sbcode']
            rno = request.form['rno']
            cur = mysql.connection.cursor()
            cur.execute("""
                UPDATE lecturer
                SET name=% s,gender=% s,email=% s,sbcode=% s,rno=%s
                WHERE lsn=%s
                """, (name,gender,email,sbcode,rno,lsn))
            mysql.connection.commit()
            flash("Data Updated Successfully")
            return redirect(url_for('tindex'))
    except:
        flash("Data Updation Unsuccessfully")
        return redirect(url_for('tindex'))

#--------------------------------Student Start-----------------------------------------#

@app.route('/stindex')
def stindex():
    cur = mysql.connection.cursor()
    cur.execute("SELECT  * FROM student")
    data = cur.fetchall()
    cur.close()
    return render_template('stindex.html', system=data )


@app.route('/stinsert', methods = ['POST'])
def stinsert():
    try:
        if request.method == "POST":
            usn = request.form['usn']
            stname = request.form['stname']
            stgender = request.form['stgender']
            stemail = request.form['stemail']
            stdob = request.form['stdob']
            stsem = request.form['stsem']
            stphone = request.form['stphone']
            sbcode = request.form['sbcode']
            lsn = request.form['lsn']
            cur = mysql.connection.cursor()
            cur.execute("INSERT INTO student (usn,stname,stgender,stemail,stdob,stsem,stphone,sbcode,lsn) VALUES (%s, %s, %s, %s, %s,%s, %s, %s, %s)", (usn,stname,stgender,stemail,stdob,stsem,stphone,sbcode,lsn))
            mysql.connection.commit()
            flash("Data Inserted Successfully")
            return redirect(url_for('stindex'))
    except:
        flash("Data Insertion Unsuccessfully")
        return redirect(url_for('stindex'))



@app.route('/stdelete/<string:id_data>', methods = ['GET'])
def stdelete(id_data):
    try:
        cur = mysql.connection.cursor()
        cur.execute("DELETE FROM student WHERE usn=%s", (id_data,))
        mysql.connection.commit()
        flash("Record Has Been Deleted Successfully")
        return redirect(url_for('stindex'))
    except:
        flash("Record Has Not Been Deleted Successfully")
        return redirect(url_for('stindex'))



@app.route('/stupdate',methods=['POST','GET'])
def stupdate():
    try:
        if request.method == 'POST':
            usn = request.form['usn']
            stname = request.form['stname']
            stgender = request.form['stgender']
            stemail = request.form['stemail']
            stdob = request.form['stdob']
            stsem = request.form['stsem']
            stphone = request.form['stphone']
            sbcode = request.form['sbcode']
            lsn = request.form['lsn']
            cur = mysql.connection.cursor()
            cur.execute("""
                UPDATE student
                SET stname=% s,stgender=% s,stemail=% s,stdob=% s,stsem=% s,stphone=% s,sbcode=% s,lsn=% s
                WHERE usn=%s
                """, (stname,stgender,stemail,stdob,stsem,stphone,sbcode,lsn,usn))
            mysql.connection.commit()
            flash("Data Updated Successfully")
            return redirect(url_for('stindex'))
    except:
        flash("Data Updation Unsuccessfully")
        return redirect(url_for('stindex'))

#--------------------------------Query Start-----------------------------------------#

@app.route('/qindex')
def qindex():
    cur = mysql.connection.cursor()
    cur.execute("SELECT  * FROM queries")
    data = cur.fetchall()
    cur.close()
    return render_template('qindex.html', system=data )


@app.route('/qinsert', methods = ['POST'])
def qinsert():
    try:
        if request.method == "POST":
            sno = request.form['sno']
            usn = request.form['usn']
            queries = request.form['queries']
            cur = mysql.connection.cursor()
            cur.execute("INSERT INTO queries (sno,usn,queries) VALUES (%s, %s, %s)", (sno, usn,queries))
            mysql.connection.commit()
            flash("Thank for Contacting us..we will resolve your query at the prior preference...")
            return render_template('index.html')
    except:
        flash("Query not submitted.. Please Try again..")
        return render_template('index.html')    


@app.route('/qdelete/<string:id_data>', methods = ['GET'])
def qdelete(id_data):
    try:
        cur = mysql.connection.cursor()
        cur.execute("DELETE FROM queries WHERE sno=%s", (id_data,))
        mysql.connection.commit()
        flash("Record Has Been Deleted Successfully")
        return redirect(url_for('qindex'))
    except:
        flash("Record Has Not Been Deleted Successfully")
        return redirect(url_for('qindex'))


#--------------------------------Main Start-----------------------------------------#

if __name__ == "__main__":
    app.run(debug=True)

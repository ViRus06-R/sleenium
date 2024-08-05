# Necessary imports

from flask import Flask, render_template_string, request
from selenium import webdriver
from selenium.webdriver.common.by import By
from selenium.webdriver.support.ui import WebDriverWait
from selenium.webdriver.support import expected_conditions as EC
import time
from sympy import false, true
from twocaptcha import TwoCaptcha

# Initialize 2Captcha solver with your API key
solver = TwoCaptcha('Your api key')

app = Flask(__name__)

time_interval = 0.0001 
titles = [
    '504 Gateway Time-out', '403 Forbidden', 'Problem loading page', '503 Service Temporarily Unavailable', 
    'Service Unavailable', '500 Internal Server Error', 'Database error', 'FastCGI Error', 
    'The connection has timed out', 'Problemas al cargar la página', 'Error 502 (Server Error)!!1'
]
heading_texts = [
    '502 Bad Gateway', 'Service Unavailable', '403 ERROR', 'Error 503 Service Unavailable', 
    '404 Not Found', '504 Gateway Time-out', 'This page isn’t working'
]
my_body = ['Scheduled maintenance is under progress']

def refresh_if_needed(driver):
    my_title = driver.title
    if len(driver.find_elements(By.TAG_NAME, 'body')[0].find_elements(By.XPATH, "./*")) <= 1 or my_title in titles:
        print("Error detected in title. Refreshing...")
        time.sleep(time_interval)
        driver.refresh()
    else:
        h1_elements = driver.find_elements(By.TAG_NAME, 'h1')
        if h1_elements and h1_elements[0].text in heading_texts:
            print("Error detected in heading text. Refreshing...")
            time.sleep(time_interval)
            driver.refresh()

def check_and_click_available_date(driver):
    while True:
        try:
            refresh_if_needed(driver)
            # Wait for the datepicker to be vis
            EC.visibility_of_element_located((By.CLASS_NAME, "datepicker-days"))
            

            # Get all date elements in the datepicker
            date_elements = driver.find_elements(By.XPATH, "//td[contains(@class, 'day')]")

            for date_element in date_elements:
                if "disabled" not in date_element.get_attribute("class"):
                    date_element.click()
                    print("Available date clicked")
                    return
                
        except Exception as e:
            print(f"An error occurred: {e}")
            driver.refresh()
            print("No available dates found. Retrying...")
        time.sleep(0.2)  # Wait before retrying

def solve_captcha(captcha_element):
    captcha_src = captcha_element.get_attribute('src')
    result = solver.normal(captcha_src)
    return result['code']

def solve_recaptcha(site_key, url):
    result = solver.recaptcha(sitekey=site_key, url=url)
    return result['code']

def click_element_with_retry(driver, by, value):
    while True:
        try:
            refresh_if_needed(driver)
            element = WebDriverWait(driver, 0.2).until(
                EC.element_to_be_clickable((by, value))
            )
            element.click()
            return
        except Exception as e:
            print(f"Click failed: {e}. Retrying...")
            

@app.route('/', methods=['GET', 'POST'])
def main():
    if request.method == 'POST':
        first_name = request.form['first_name']
        last_name = request.form['last_name']
        username = request.form['username']
        password = request.form['password']
        application_centre = request.form['application_centre']
        service_type = request.form['service_type']
        applicant_type = request.form['applicant_type']
        card = request.form['card']
        

        while True:
            driver = webdriver.Chrome()
            driver.get("https://blsitalypakistan.com/account/login")

            try:
                refresh_if_needed(driver)
                login_try = False
                while not login_try:
                    driver.find_element(By.XPATH, "//input[@placeholder='Enter Email']").send_keys(username)
                    driver.find_element(By.XPATH, "//input[@placeholder='Enter Password']").send_keys(password)
                    captcha_element = driver.find_element(By.XPATH, "/html/body/div[6]/div/div/div/div/div[2]/form/div[5]/img")
                    captcha_code = solve_captcha(captcha_element)
                    driver.find_element(By.XPATH, "/html/body/div[6]/div/div/div/div/div[2]/form/div[6]/input").send_keys(captcha_code)
                    driver.find_element(By.XPATH, "/html/body/div[6]/div/div/div/div/div[2]/form/div[8]/button").click()
                    if(driver.current_url == "https://blsitalypakistan.com/account/login"):
                        driver.refresh()
                    if(driver.current_url == "https://blsitalypakistan.com/account/account_details"):
                        login_try = True
                    
                
                refresh_if_needed(driver)
                driver.find_element(By.XPATH, "/html/body/div[5]/div/div/div[1]/div/ul/li[4]/a").click()
                
                #click_element_with_retry(driver, By.NAME, "/html/body/div[6]/a/img")

                if application_centre == "Islamabad":
                    click_element_with_retry(driver, By.XPATH, "/html/body/div[13]/div/div/div[2]/div/form/div[1]/select/option[2]")
                    click_element_with_retry(driver, By.NAME, "valCenterLocationTypeId")
                    click_element_with_retry(driver, By.XPATH, "//html/body/div[11]/div/div/div[2]/div/form/div[2]/select/option[9]")
                if application_centre == "Lahore":
                    click_element_with_retry(driver, By.XPATH, "/html/body/div[13]/div/div/div[2]/div/form/div[1]/select/option[3]")
                    click_element_with_retry(driver, By.NAME, "valCenterLocationTypeId")
                    click_element_with_retry(driver, By.XPATH, "/html/body/div[11]/div/div/div[2]/div/form/div[2]/select/option[5]")
                if application_centre == "Faisalabad":
                    click_element_with_retry(driver, By.XPATH, "/html/body/div[13]/div/div/div[2]/div/form/div[1]/select/option[5]")
                    click_element_with_retry(driver, By.NAME, "valCenterLocationTypeId")
                    click_element_with_retry(driver, By.XPATH, "/html/body/div[11]/div/div/div[2]/div/form/div[2]/select/option[4]") 
                if application_centre == "Multan":
                    click_element_with_retry(driver, By.XPATH, "/html/body/div[13]/div/div/div[2]/div/form/div[1]/select/option[6]")
                    click_element_with_retry(driver, By.NAME, "valCenterLocationTypeId")
                    click_element_with_retry(driver, By.XPATH, "/html/body/div[11]/div/div/div[2]/div/form/div[2]/select/option[4]") 
                refresh_if_needed(driver)
                click_element_with_retry(driver, By.NAME, "valAppointmentForMembers")
                click_element_with_retry(driver, By.XPATH, "/html/body/div[11]/div/div/div[2]/div/form/div[3]/select/option[2]")
                refresh_if_needed(driver)
                boom = True
                while boom:
                    try:
                        # Locate captcha element and solve it
                        captcha_element = driver.find_element(By.XPATH, "/html/body/div[11]/div/div/div[2]/div/form/div[4]/div/img")
                        captcha_code = solve_captcha(captcha_element)
                        driver.find_element(By.XPATH, "/html/body/div[11]/div/div/div[2]/div/form/div[5]/input").send_keys(captcha_code)
        
                        # Click the element to open the date picker
                        click_element_with_retry(driver, By.NAME, "valAppointmentDate")
        
                        # Wait for the date picker to be visible
                        WebDriverWait(driver, 0.002).until(
                            EC.visibility_of_element_located((By.CLASS_NAME, "datepicker-days"))
                            )

                        # Get all date elements in the date picker
                        date_elements = driver.find_elements(By.XPATH, "//td[contains(@class, 'day')]")

                        for date_element in date_elements:
                            if "disabled" not in date_element.get_attribute("class"):
                                date_element.click()
                                print("Available date clicked")
                                boom = False  # Exit the loop once a date is clicked
                        if(not driver.find_element(By.XPATH, "/html/body/div[11]/div/div/div[2]/div/form/div[8]/div/select/option[2]")):
                            driver.refresh()  
        
                        if boom:  # If no available date is clicked, refresh the page and try again
                            print("No available dates found. Retrying...")
                            driver.refresh()
                        

                    except :
                        
                        print("Retrying due to error...")
                    
                    
                            
                        
                    
                click_element_with_retry(driver, By.XPATH, "/html/body/div[11]/div/div/div[2]/div/form/div[8]/div/select/option[2]")
                driver.find_element(By.XPATH, "/html/body/div[11]/div/div/div[2]/div/form/div[9]/div[5]/input").send_keys(first_name)
                    
                driver.find_element(By.XPATH, "/html/body/div[11]/div/div/div[2]/div/form/div[9]/div[6]/input").send_keys(last_name)

                    

                    # Solve reCAPTCHA v2
                    #recaptcha code not working?help in python code?
                site_key = "6LcQb8klAAAAAHDDKtB3PaB6gvbh-ej4qa8BRKV9"
                url = driver.current_url
                recaptcha_response = solve_recaptcha(site_key, url)
                driver.execute_script(f'document.getElementById("g-recaptcha-response").innerHTML = "{recaptcha_response}";')
                driver.find_element(By.XPATH ,"/html/body/div[11]/div/div/div[2]/div/form/div[9]/p/input").click()
                driver.find_element(By.XPATH ,"/html/body/div[11]/div/div/div[2]/div/form/div[9]/div[11]/button").click()
                
                if(driver.find_element(By.XPATH , "/html/body/div[1]/div/div[2]/div[1]/div[2]/div/h1")):
                    driver.find_element(By. XPATH, "/html/body/div[1]/div/div[2]/div[1]/form/div[5]/div[3]/div[1]/div[1]/input").send_keys(card)
                    driver.find_element(By.XPATH, "/html/body/div[1]/div/div[2]/div[1]/form/div[5]/div[3]/div[2]/div[1]/div/ul/li[6]/span").click
                    driver.find_element(By.XPATH , "/html/body/div[1]/div/div[2]/div[1]/form/div[5]/div[3]/div[2]/div[2]/div/ul/li[4]/span").click()
                # code for card details
                   
               
            except Exception as e:
                print(f"An error occurred: {e}")
            finally:
                time.sleep(160)  # Pause before restarting
                

    return render_template_string('''
        <h1>Book Appointment</h1>
        <form method="post">
            <label for="first_name">First Name:</label><br>
            <input type="text" id="first_name" name="first_name"><br>
            <label for="last_name">Last Name:</label><br>
            <input type="text" id="last_name" name="last_name"><br>
            <label for="username">Username:</label><br>
            <input type="text" id="username" name="username"><br>
            <label for="password">Password:</label><br>
            <input type="password" id="password" name="password"><br>
            <label for="application_centre">Application Centre:</label><br>
            <select id="application_centre" name="application_centre">
                <option value="Islamabad">Islamabad</option>
                <option value="Lahore">Lahore</option>
                <option value="Faisalabad">Faisalabad</option>
                <option value="Multan">Multan</option>
            </select><br>
            <label for="service_type">Service Type:</label><br>
            <select id="service_type" name="service_type">
                <option value="National - work">National - work</option>
                <option value="National Family reunion">National Family reunion</option>
            </select><br>
            <label for="applicant_type">Applicant Type:</label><br>
            <select id="applicant_type" name="applicant_type">
                <option value="Individual">Individual</option>
            </select><br>
            <label for="card">card:</label><br>
            <input type="numbers" id="card" name="card"><br>
            </select><br>
            <input type="submit" value="Book Now">
            
        </form>
    ''')

if __name__ == '__main__':
    app.run(debug=True)

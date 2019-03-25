1、php artisan make:mail SendEmailTest
---------------------------------------------------------------------
   app/Mail/SendEmailTest.php
   <?php
	  
	namespace App\Mail;
	  
	use Illuminate\Bus\Queueable;
	use Illuminate\Mail\Mailable;
	use Illuminate\Queue\SerializesModels;
	use Illuminate\Contracts\Queue\ShouldQueue;
	  
	class SendEmailTest extends Mailable
	{
	    use Queueable, SerializesModels;
	  
	    /**
	     * Create a new message instance.
	     *
	     * @return void
	     */
	    public function __construct()
	    {
	          
	    }
	  
	    /**
	     * Build the message.
	     *
	     * @return $this
	     */
	    public function build()
	    {
	        return $this->view('emails.test');
	    }
	}
---------------------------------------------------------------------
	resources/views/emails/test.blade.php
	<!DOCTYPE html>
	<html>
	<head>
		<title>How to send mail using queue in Laravel 5.7? - ItSolutionStuff.com</title>
	</head>
	<body>
	   
	<center>
	<h2 style="padding: 23px;background: #b3deb8a1;border-bottom: 6px green solid;">
		<a href="https://itsolutionstuff.com">Visit Our Website : ItSolutionStuff.com</a>
	</h2>
	</center>
	  
	<p>Hi, Sir</p>
	<p>Lorem ipsum dolor sit amet, consectetur adipisicing elit, sed do eiusmod
	tempor incididunt ut labore et dolore magna aliqua. Ut enim ad minim veniam,
	quis nostrud exercitation ullamco laboris nisi ut aliquip ex ea commodo
	consequat. Duis aute irure dolor in reprehenderit in voluptate velit esse
	cillum dolore eu fugiat nulla pariatur. Excepteur sint occaecat cupidatat non
	proident, sunt in culpa qui officia deserunt mollit anim id est laborum.</p>
	  
	<strong>Thank you Sir. :)</strong>
	  
	</body>
	</html>
---------------------------------------------------------------------
    .env
    AIL_DRIVER=smtp
	MAIL_HOST=smtp.gmail.com
	MAIL_PORT=587
	MAIL_USERNAME=xyz@gmail.com
	MAIL_PASSWORD=123456
	MAIL_ENCRYPTION=tls
---------------------------------------------------------------------
	.env
	QUEUE_CONNECTION=database
	php artisan queue:table
	php artisan migrate
---------------------------------------------------------------------
	php artisan make:job SendEmailJob
	app/Jobs/SendEmailJob.php
	<?php
	  
	namespace App\Jobs;
	   
	use Illuminate\Bus\Queueable;
	use Illuminate\Queue\SerializesModels;
	use Illuminate\Queue\InteractsWithQueue;
	use Illuminate\Contracts\Queue\ShouldQueue;
	use Illuminate\Foundation\Bus\Dispatchable;
	use App\Mail\SendEmailTest;
	use Mail;
	   
	class SendEmailJob implements ShouldQueue
	{
	    use Dispatchable, InteractsWithQueue, Queueable, SerializesModels;
	  
	    protected $details;
	  
	    /**
	     * Create a new job instance.
	     *
	     * @return void
	     */
	    public function __construct($details)
	    {
	        $this->details = $details;
	    }
	   
	    /**
	     * Execute the job.
	     *
	     * @return void
	     */
	    public function handle()
	    {
	        $email = new SendEmailTest();
	        Mail::to($this->details['email'])->send($email);
	    }
	}	
---------------------------------------------------------------------

	routes/web.php
	Route::get('email-test', function(){
	  
		$details['email'] = 'your_email@gmail.com';
	  
	    dispatch(new App\Jobs\SendEmailJob($details));
	  
	    dd('done');
	});	
---------------------------------------------------------------------
	php artisan queue:listen

	http://localhost:8000/email-test


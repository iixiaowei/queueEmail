1、php artisan make:mail SendEmailTest<br>
---------------------------------------------------------------------<br>
   app/Mail/SendEmailTest.php<br>
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
	}<br>
---------------------------------------------------------------------<br>
	resources/views/emails/test.blade.php
    Thank you Sir. :) <br>
---------------------------------------------------------------------<br>
    .env
    AIL_DRIVER=smtp
	MAIL_HOST=smtp.gmail.com
	MAIL_PORT=587
	MAIL_USERNAME=xyz@gmail.com
	MAIL_PASSWORD=123456
	MAIL_ENCRYPTION=tls<br>
---------------------------------------------------------------------<br>
	.env
	QUEUE_CONNECTION=database
	php artisan queue:table
	php artisan migrate<br>
---------------------------------------------------------------------<br>
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
	}	<br>
---------------------------------------------------------------------<br>

	routes/web.php
	Route::get('email-test', function(){
	  
		$details['email'] = 'your_email@gmail.com';
	  
	    dispatch(new App\Jobs\SendEmailJob($details));
	  
	    dd('done');
	});	<br>
---------------------------------------------------------------------<br>
	php artisan queue:listen

	http://localhost:8000/email-test


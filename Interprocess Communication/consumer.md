```c
/* consumers - c = 0, 1, ..., M-1 */
void consumer(unsigned int c)
{
	DATA data;
	forever
	{
		bool done = false;
		do
		{
			lock(c);
			if (fifo.notEmpty())
			{
				fifo.retrieve(&data);
				done = true;
			}
			unlock(c);
		} while (!done);
		consume_data(data);
		do_something_else();
	}
}```

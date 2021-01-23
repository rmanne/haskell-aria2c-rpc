# aria2c-rpc

A library for interfacing to aria2c through websockets. The library will not start aria2c, but rather, will expect that aria2c is already running.

Example usage:

    import qualified Network.Aria2 as Aria2
    import Control.Concurrent.Chan(newChan, getChanContents, writeChan)
    import Control.Monad(mapM_)
    import Control.Concurrent(forkIO)
    
    main :: IO ()
    main = do
        aria2InputChan <- newChan
        aria2OutputChan <- newChan
        _ <- forkIO (Aria2.run Aria2.defaultOptions { Aria2.secret = Just "asdf" } aria2InputChan aria2OutputChan)
        writeChan aria2InputChan ("1", "https://google.com")
        writeChan aria2InputChan ("2", "https://www.google.com")
        writeChan aria2InputChan ("3", "https://doesnotexist.doesnotexist")
        getChanContents aria2OutputChan >>= mapM_ print


Output for the example:

    ("3",Left "CUID#44 - Name resolution for doesnotexist.doesnotexist failed:Domain name not found")
    ("2",Right "/home/tmp/index.20.html")
    ("1",Right "/home/tmp/index.21.html")
    ...: threadWait: invalid argument (Bad file descriptor)
    ...: thread blocked indefinitely in an MVar operation

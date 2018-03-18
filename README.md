
# Table of Contents

1.  [Haskell >>= Elm (Haskell Bind Elm)](#orgd649a50)
    1.  [Design (Nothing of this exists)](#orgcd596a4)
        1.  [Values](#orge6c6462)
        2.  [How to use it](#org076bf11)
        3.  [Layer One](#org7c80716)
        4.  [Layer Two](#org918e447)
        5.  [Layer Three](#org5a0839e)


<a id="orgd649a50"></a>

# Haskell >>= Elm (Haskell Bind Elm)

The goal of the project is to provide seamless integration of Haskell and Elm in a project. The goal has 3 separate layers: 

1.  Generating Elm data types out of Haskell data types, reflecting module structure.
2.  Generating decoders and encoders so that backend and frontend can communicate seamlessly.
3.  Generating Elm-Servant Client as a module that hides complexity of frontend <-> backend communication


<a id="orgcd596a4"></a>

## Design (Nothing of this exists)


<a id="orge6c6462"></a>

### Values

1.  Predictable behavior

2.  Friendly Error Messages

3.  We are here to reduce stress of the user


<a id="org076bf11"></a>

### How to use it

Install it, build it, add it as dependency.
Hook it up with your build system.


<a id="org7c80716"></a>

### Layer One

Then lets make a simple usage example of Layer One.

    -- >>= Elm --- <<< THIS IS IMPORTANT COMMENT THAT HELPS US IDENTIFY WHICH FILES TO TRY TO CONVERT
    module Data.Example 
      ( Sljiva
      , Fruit(..)
      ) where
    
    import Data.TreeSeed
    import Data.Color
    import qualified Data.Text as T
    import qualified Data.ByteString as BS
    
    data Sljiva = Sljiva
      { radius  :: Int
      , weight  :: Double 
      , name    :: T.Text
      , licence :: BS.ByteString
      , seed    :: TreeSeed
      }
    
    data Fruit
      = Banana
      | Raspberry
      | Lemon Color
      deriving(Show, Generic)
    
    data Being a b = Being 
      { a :: a
      , b :: b
      } deriving (Eq, Show)
    
    type PrinterTags = [String]
    
    newtype SweetFruit = SweetFruit { unSweetFruit :: Fruit }

After running from CLI in project root

    hs-bind-elm [opts ...]

This will generate Elm files according the module structure of the designated file:

    -- >>= Elm -- This comment will designate that the file has been generated with hs-bind-elm
    module Data.Example exposing (Sljiva, Fruit)
    
    import Data.TreeSeed
    import Data.Color
    
    type alias Sljiva =
      { radius  : Int
      , weight  : Float
      , name    : String
      , licence : String
      , seed    : TreeSeed
      } 
    
    type Fruit
      = Banana
      | Raspberry
      | Lemon Color
    
    type alias Being a b =
      { a : a
      , b : b
      }
    
    
    type alias PrinterTags = List String
    
    type alias SweetFruit 
      = { unSweetFruit : Fruit }
    
    unSweetFruit : SweetFruit -> Fruit
    unSweetFruit a = 
      a.unSweetFruit

Notice that there are couple of important features:

1.  Reduction to String

    All data types that can represent String will be String in Elm files. Text, ByteString, String, CString will become all Strings

2.  Replacing Imports

    The tool will lookup imports, if modules can be read, we will convert needed bits to Elm. 
    If module is from outside dependency, we provide way of mapping Haskell types into Elm types, in configuration file.

3.  Exposing same types

    We try to remove barrier of thinking, when converting from Haskell to Elm.

4.  New types are records with `un` prefix

    This way there is smaller runtime impact on the unwrapping the value of underlaying type.
    would require `casing` everytime we want to peak into the value of hidden type.


<a id="org918e447"></a>

### TODO Layer Two

TBD


<a id="org5a0839e"></a>

### TODO Layer Three

TBD

